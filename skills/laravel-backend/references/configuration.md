# Configuration

Hard rule: `env()` belongs only in configuration/bootstrap locations where Laravel expects it. Normal application code—Controllers, Actions, Services, Jobs, Models, and commands—uses `config()`.

Put environment-specific infrastructure and secrets in `.env` plus config files. Admin-editable or runtime business settings generally belong in persistent application storage, not `.env`. Keep `.env.example` complete and free of real secrets. When adding config, account for config caching and document required environment keys.
