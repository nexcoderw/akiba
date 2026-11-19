# Akiba

Akiba is a multi-tenant inventory SaaS foundation. Its repositories separate platform administration, organization-facing experiences, application services, and database ownership. The current foundation includes identity, access control, organization management, and auditing; the database documentation identifies core inventory domains as planned work.

This repository is the workspace entry point for [Akiba](https://github.com/nexcoderw/akiba). It connects four independent repositories through Git submodules. Each submodule retains its own history, contribution rules, dependencies, and release workflow. The parent repository records a specific commit for each component rather than combining their histories.

## Repositories

| Repository | Root path | Responsibility |
| --- | --- | --- |
| [akiba-api](https://github.com/nexcoderw/akiba-api) | `akiba-api/` | NestJS and TypeScript HTTP API, authentication, authorization, tenant resolution, and application workflows. Consumes the canonical database package. |
| [akiba-db](https://github.com/nexcoderw/akiba-db) | `akiba-db/` | Canonical PostgreSQL schema, Prisma migrations and generated client, and shared database package APIs. |
| [akiba-admin](https://github.com/nexcoderw/akiba-admin) | `akiba-admin/` | Next.js platform administration frontend, including platform access and organization administration. |
| [akiba-organization](https://github.com/nexcoderw/akiba-organization) | `akiba-organization/` | Designated organization-facing frontend repository. At the pinned revision, its README and application routes still describe platform administration; organization-specific behavior is not established by this workspace setup. |

## Architecture

The intended component relationship is:

```text
Platform administrators                 Organization users
          |                                     |
     akiba-admin                       akiba-organization
          |                          (designated portal)
          +------------------+------------------+
                             |
                         akiba-api
                             |
                  akiba-db package APIs
                             |
                         PostgreSQL
```

The API owns effective authorization and business workflows. The database repository owns schema and migration changes. Frontend visibility does not replace API authorization. The organization portal connection above describes its intended role, not verified integration at the pinned revision.

This workspace supplies repository composition, not a shared build system or deployment pipeline. Submodule registration does not configure services, provision databases, or apply migrations.

## Clone the workspace

Clone the parent repository and initialize all pinned submodules:

```sh
git clone --recurse-submodules https://github.com/nexcoderw/akiba.git
cd akiba
```

For an existing clone:

```sh
git submodule update --init --recursive
```

Access to the parent repository does not grant access to its submodules. Authenticate to GitHub over HTTPS with an account authorized for each repository; keep credentials out of repository URLs and tracked files.

The resulting layout is:

```text
akiba/
├── .gitmodules
├── README.md
├── akiba-api/
├── akiba-db/
├── akiba-admin/
└── akiba-organization/
```

## Work with pinned revisions

After pulling changes to the parent repository, synchronize the submodule checkouts:

```sh
git pull --ff-only
git submodule update --init --recursive
git submodule status --recursive
```

Submodules normally check out their recorded commits in detached HEAD state. Preserve local work before changing revisions. Use the default update command to reproduce the recorded workspace; `git submodule update --remote` instead selects upstream revisions and should be a deliberate, reviewed dependency update.

To review the workspace state:

```sh
git status --short
git diff --submodule=log
git diff --cached --submodule=log
```

An intentional component update requires an existing reviewed commit in that component repository, followed by staging its submodule path and committing the pointer change in this parent repository. Publishing a parent commit does not publish submodule commits. Follow the applicable review and approval process before pushing either repository.

## Component setup and current integration notes

Read each component's `AGENTS.md`, `.agents/README.md`, and applicable rules before working inside it. Component setup and verification instructions remain in their own READMEs:

- [API setup](akiba-api/README.md)
- [Database package setup](akiba-db/README.md)
- [Platform administration setup](akiba-admin/README.md)
- [Organization repository documentation](akiba-organization/README.md)

At the pinned API revision, `@nexcoderw/akiba-db` uses `file:../db`, and its README assumes the older `api/api` and `api/db` layout. The root names requested here are `akiba-api` and `akiba-db`, so cloning these submodules alone does not satisfy that relative dependency. Resolve the layout/package integration in a separately reviewed change before attempting a workspace build. No component manifests or configuration have been rewritten here.

The organization repository currently identifies its package as `admin` and documents the platform administration frontend. Its intended portal role requires separate implementation and verification. Consult the actual component files rather than assuming the two frontend repositories already expose distinct workflows.

Keep secrets local or in the existing approved secret-management system. Follow component-specific environment, database, and deployment procedures; this parent repository introduces no replacement configuration.
