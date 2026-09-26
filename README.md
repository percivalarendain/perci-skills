# Perci Skills

Reusable development skills, conventions, and engineering standards for AI coding agents such as Claude Code and OpenAI Codex.

The goal of this repository is to provide consistent development patterns across projects while keeping AI context focused and reducing unnecessary token usage through modular, progressively loaded skills.

## Skills

Skills are organized by technology and responsibility.

### Available

#### Laravel Backend

`skills/laravel-backend/`

Backend architecture and development standards for Laravel applications.

Covers:

- Architecture and layer responsibilities
- Controllers and Form Requests
- Actions and Services
- Models and Queries
- Authorization
- Exceptions and error handling
- Database transactions
- Migrations
- Factories and Seeders
- Jobs, Redis, and Horizon
- Events and Listeners
- Caching
- Logging
- Files and Storage
- Versioned APIs
- Configuration
- Artisan Commands and Scheduler
- Notifications and Mail
- Identifiers and reference numbers
- Date and timezone handling
- Money handling
- Testing

### Planned

Additional skills may include:

- Inertia.js
- React
- Vue
- Tailwind CSS
- Pest
- Filament
- Docker and development tooling

These skills can be combined depending on the project's technology stack.

For example:

```text
Laravel + Inertia + React + Tailwind
```

may use:

```text
laravel-backend
inertia
react
tailwind
```

while:

```text
Laravel + Inertia + Vue + Tailwind
```

may use:

```text
laravel-backend
inertia
vue
tailwind
```

## Installation

Skills are directory-based. Copy the skill directory into the project where the agent should use it.

### Claude Code

```bash
mkdir -p .claude/skills
cp -R /path/to/perci-skills/skills/laravel-backend .claude/skills/laravel-backend
```

### OpenAI Codex

```bash
mkdir -p .agents/skills
cp -R /path/to/perci-skills/skills/laravel-backend .agents/skills/laravel-backend
```

After installation, the project contains:

```text
.claude/
└── skills/
    └── laravel-backend/
        ├── SKILL.md
        ├── references/
        └── examples/

.agents/
└── skills/
    └── laravel-backend/
        ├── SKILL.md
        ├── references/
        └── examples/
```

Replace `/path/to/perci-skills` with the local checkout path. The skill remains project-local so each project can use the standards appropriate to its stack and requirements.

## Design Principles

- Prefer framework-native solutions before custom abstractions.
- Inspect the existing project before introducing new conventions.
- Follow established project conventions unless intentionally changing them.
- Use the smallest architecture that clearly solves the problem.
- Avoid unnecessary abstraction and over-engineering.
- Keep each skill focused on a specific technology or responsibility.
- Load detailed references only when they are relevant to the current task.
- Keep shared skills agent-neutral where possible.

## Repository Structure

```text
perci-skills/
├── skills/
│   ├── laravel-backend/
│   │   ├── SKILL.md
│   │   ├── references/
│   │   └── examples/
│   │
│   ├── inertia/          # planned
│   ├── react/            # planned
│   ├── vue/              # planned
│   └── tailwind/         # planned
│
└── templates/
    ├── CLAUDE.md
    └── AGENTS.md
```

## AI Coding Agents

The skills are intended to work with AI coding tools including:

- Claude Code
- OpenAI Codex

Agent-specific project instructions should remain lightweight. Detailed engineering standards belong in the reusable skills.

## Progressive Disclosure

Each skill uses a small `SKILL.md` as its entry point.

Detailed standards are separated into reference files so an AI agent can load only the information needed for the current task instead of loading the entire development standard.

Example:

```text
laravel-backend/
├── SKILL.md
└── references/
    ├── actions.md
    ├── models.md
    ├── migrations.md
    ├── authorization.md
    ├── caching.md
    └── testing.md
```

A migration task should not need to load documentation about queues, notifications, caching, or unrelated frontend technologies.

## Status

Currently focused on building the Laravel Backend skill.

Additional frontend and development-tooling skills will be added incrementally.
