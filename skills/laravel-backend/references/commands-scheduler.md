# Commands and scheduler

Artisan Commands are thin CLI entry points. Use `{module}:{action}` names such as `employees:import` or `records:process-expired`; delegate work to Actions/Services. Add `--dry-run` to operational or destructive commands where useful, and report counts and failures clearly.

The scheduler decides when work executes. Use `withoutOverlapping()` when concurrent execution is unsafe and `onOneServer()` for multi-server schedules when appropriate. Queue long-running work. Match scheduling APIs to the installed Laravel version and test command behavior independently of the scheduler where practical.
