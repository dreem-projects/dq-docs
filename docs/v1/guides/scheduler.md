# Scheduler

The scheduler runs background and periodic tasks using **Celery** with **Redis** as the broker and result backend. It is **pluggable**: you decide which jobs exist and when they run; you define and register whatever you want. Nothing runs until you plug it in.

**Before anything can be done, Redis must be installed and configured.** Celery depends on Redis for task queue and results.

---

## Prerequisite: Install and configure Redis

### 1. Install Redis

Install Redis on your machine (or use a hosted Redis service).

- **macOS (Homebrew):** `brew install redis` then `brew services start redis` (or run `redis-server`).
- **Linux:** Use your package manager (e.g. `apt install redis-server`, `yum install redis`).
- **Docker:** `docker run -d -p 6379:6379 redis`.

Ensure the Redis server is running and reachable (default port **6379**).

### 2. Configure Redis in your app

The scheduler (and any app code that uses Redis) reads connection settings from **environment variables**. Set these in your **.env** (or export them):

| Variable        | Description                    | Example   |
|----------------|--------------------------------|-----------|
| **REDIS_HOST** | Redis server host (required).  | `localhost` or `127.0.0.1` |
| **REDIS_PORT** | Redis server port (required).  | `6379`    |
| **REDIS_PASSWORD** | Optional; omit or leave empty if Redis has no auth. | `""` or omit |

The scheduler **setup does not use defaults** for **REDIS_HOST** or **REDIS_PORT**; both must be set or the broker URL is invalid and Celery will fail to connect. In **dreema/scheduler/setup.py**, broker and backend URLs are built as `redis://[:password@]REDIS_HOST:REDIS_PORT/1` (Redis DB 1).

Example **.env** (with auth):

```bash
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=yourpassword
```

Example **.env** (no auth — omit or leave **REDIS_PASSWORD** empty):

```bash
REDIS_HOST=localhost
REDIS_PORT=6379
```

If Redis is not running or these variables are wrong, Celery workers will fail to connect and no scheduled tasks will run.

---

## How the scheduler is pluggable

- The framework provides **Celery** (the `scheduler` object), **runAsyncJob**, **configureBeatSchedule**, and helpers (**createTask**, **createAsyncTask**, **CallbackScheduler**). It does not define your application jobs (except the generic **CallbackScheduler** task).
- Define tasks (e.g. in a module like **utilities/SchedulerJobs.py**). Each task is a function decorated with **@scheduler.task(name="TaskName")** 
- Register your jobs module so Celery discovers those tasks by just importing it in **registers.py**:

  ```python
  from utilities import SchedulerJobs
  ```

  Importing the module is enough for Celery to discover your tasks when the worker loads (decorators run on import). 
- Call **configureBeatSchedule({...})** (e.g. at the bottom of your jobs module) to define periodic tasks.
- Trigger one-off tasks from controllers or other code by calling **TaskName.delay(...)**.

So: Redis + env first; then you plug in your own tasks, beat schedule, and triggers.

---

## Scheduler API (dreema.scheduler)

Import from **dreema.scheduler**:

| Export | Purpose |
|--------|---------|
| **scheduler** | The Celery app instance (broker/backend from Redis env). Use it to define tasks. |
| **runAsyncJob(coroutine)** | Runs an async coroutine from inside a Celery task (handles event loop). |
| **configureBeatSchedule(beat_schedule)** | Sets the Celery beat schedule (periodic tasks). |
| **createTask(name)** | Decorator factory: creates a **sync** Celery task with the given name. |
| **createAsyncTask(name)** | Decorator factory: creates a Celery task that runs an **async** function via **runAsyncJob**. |
| **CallbackScheduler** | A generic Celery task that runs an arbitrary async callable; useful for one-off async work by name. |


## Periodic tasks (Beat schedule)

To run a task on a fixed interval or cron, call **configureBeatSchedule** with a dict. Keys are logical names; each entry has **task** (task name string), **schedule** (seconds as float, or a Celery schedule/crontab), and optionally **args** / **kwargs**.

Example: run **RepeatableJob** every 60 seconds:

```python
from dreema.scheduler import configureBeatSchedule

configureBeatSchedule({
    "RepeatableJob": {
        "task": "RepeatableJob",
        "schedule": 60.0,
        "args": (),
    },
})
```

You can call **configureBeatSchedule** once at module load (e.g. at the bottom of **utilities/SchedulerJobs.py**) or during app init. Only one beat schedule is active per Celery app (later calls overwrite).

---

## Triggering tasks from your code

From a controller or anywhere your app runs, trigger a task by calling **.delay(...)** on the task (with args and kwargs that are JSON-serializable):

```python
from utilities.SchedulerJobs import ScheduleSMS, ScheduleEmail

# One-off: send SMS
ScheduleSMS.delay("Hello", ["+1234567890"])

# One-off: send email
ScheduleEmail.delay(message="Body", destinations=["a@b.com"], subject="Subject")
```

The task is queued to Redis; a running Celery worker will pick it up and run it.

---

## Running the worker and beat (terminal commands)

Run these commands from your **application project root** (the directory that contains your **dreema** and **utilities** packages). Ensure Redis is running and **.env** has **REDIS_HOST** and **REDIS_PORT** set.

**1. Start the Celery worker** (executes tasks from the queue):

```bash
celery -A dreema.scheduler.setup worker --loglevel=info
```

**2. For periodic (beat) schedules**, start Celery beat in a **separate terminal** (same directory):

```bash
celery -A dreema.scheduler.setup beat --loglevel=info
```

Beat pushes scheduled tasks into the queue; the worker consumes them. Keep both processes running while you need scheduled and one-off tasks.

- Use **-A dreema.scheduler.setup** so Celery loads your app’s scheduler config and task modules (e.g. **utilities.SchedulerJobs** if set in **scheduler.conf.imports**).
- To run the worker in the background: add **--detach** or use a process manager (e.g. systemd, supervisord).

---

## Summary

| Step | What you do |
|------|-------------|
| 1. Redis | Install Redis; set **REDIS_HOST**, **REDIS_PORT**, and optionally **REDIS_PASSWORD** in .env. |
| 2. Define tasks | In your own module (e.g. **utilities/SchedulerJobs.py**), define functions with **@scheduler.task(name="...")** or **createTask** / **createAsyncTask**; use **runAsyncJob** for async code inside a task. |
| 3. Register jobs | Import the jobs module at startup or set **scheduler.conf.imports** so Celery discovers your tasks. |
| 4. Beat (optional) | Call **configureBeatSchedule({...})** to define periodic tasks. |
| 5. Run | Start Redis, then `celery -A <scheduler.setup.module> worker`, and if needed `celery -A <...> beat`. |
| 6. Trigger | From controllers or other code, call **TaskName.delay(...)** to enqueue one-off work. |

The scheduler is pluggable: you decide and plug whatever tasks and schedules you want; the framework only provides the Celery app, Redis-backed broker/backend, and helpers. Without Redis installed and configured, nothing can be done.
