# Exceptions and error handling

Distinguish validation, authentication, authorization, not-found, business conflict, and unexpected failures. Use a business/domain exception when an operation cannot proceed and explicit semantics improve handling; do not create custom exceptions for every branch.

Centralize web/API rendering according to the installed Laravel version and existing exception handler. Return stable public messages and correct status codes. In production never expose stack traces, SQL, secrets, internal paths, or provider details. Log unexpected failures once with useful context; do not re-log the same exception at every layer.
