# Files

File uploads are handled via multipart requests and the request body.

## Multipart and FileParser

- The framework supports **multipart** request bodies (e.g. form-data with files).
- **FileParser** (or equivalent) may be used to parse and validate file parts. Check dreema requests or helpers for the exact API.

## request.body() and files

- **request.body()** can return parsed form data including file fields. File content may be available as bytes or a file-like object; field names and structure depend on implementation.
- Use the same validation flow as for JSON: validate required fields and file size/type in the controller, then store or process the file (e.g. save to disk or cloud storage). The ORM is for structured data; file storage is typically custom code.

This section is optional for minimal docs; expand when the file API is stable.
