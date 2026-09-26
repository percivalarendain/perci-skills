# Events and listeners

Events describe meaningful facts that already happened and should use past-tense names, such as `DepartmentCreated`. Listeners react to those facts and may be queued when appropriate.

Use events when decoupled reactions have concrete value, such as audit, integration, or independent notifications. Do not emit events merely to avoid calling an Action or Service directly. If a listener depends on committed state, use after-commit behavior supported by the installed Laravel version and test event/listener behavior without making tests brittle.
