# Files and storage

Use Laravel `Storage` directly first. Keep files private by default unless public access is intentional and secured. Validate uploads with FormRequests, generate storage filenames/keys, store relative disk paths in the database, and preserve original names only as metadata when useful.

Create a storage Service only when application-specific reusable behavior exists, such as virus scanning, deterministic naming, metadata, and cleanup coordinated across use-cases. Treat database and filesystem consistency as a workflow concern; do not assume a database rollback removes an already-written file. Avoid exposing untrusted paths and use temporary URLs for private downloads where supported.
