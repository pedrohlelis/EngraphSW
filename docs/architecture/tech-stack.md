# Technology Stack

## Related Documents

* [ADR-001 Modular Monolith Architecture](../adr/001-modular-monolith.md) — the architectural strategy this stack implements
* [ADR-006 Canvas and Visualization Technology](../adr/006-canvas-technology.md) — React Flow decision record
* [ADR-007 Client State Management](../adr/007-state-management.md) — Zustand decision record
* [Module Conventions](module-conventions.md) — how the stack is organized across modules
* [AI Architecture](ai-architecture.md) — AI SDK usage patterns

---

## Purpose

This document defines the pinned technology decisions for EngraphSW. It is the canonical reference for scaffolding the project. All versions listed here are the target versions for initial implementation. Patch versions should be verified against the npm registry at implementation time; only major and minor versions are normative.

---

## Runtime

| Technology | Version | Notes |
|---|---|---|
| Node.js | 22 LTS | Active LTS line. Use `.nvmrc` or `.node-version` to pin the project. |
| Package manager | pnpm 9.x | Workspace support, fast installs, strict hoisting. Use `pnpm-lock.yaml`. |

---

## Core Framework

| Technology | Package | Version | Notes |
|---|---|---|---|
| Next.js | `next` | 15.x | App Router only. Pages Router is not used. |
| React | `react`, `react-dom` | 19.x | Concurrent features required for canvas rendering. |
| TypeScript | `typescript` | 5.x | Strict mode enabled. `strict: true` in `tsconfig.json`. |

### Next.js Configuration

- App Router: all routes live under `app/`
- Route Handlers: used for API endpoints (no separate Express server)
- Server Components: default for layout and data-fetching components
- Client Components: marked `"use client"` only when interactivity requires it

---

## Canvas and Graph Visualization

| Technology | Package | Version | Notes |
|---|---|---|---|
| React Flow | `@xyflow/react` | 12.x | Open-source tier. Pro features not required for Phase 3. |

See [ADR-006](../adr/006-canvas-technology.md) for the decision record.

---

## Client State Management

| Technology | Package | Version | Notes |
|---|---|---|---|
| Zustand | `zustand` | 5.x | With `immer` and `devtools` middleware. |
| Immer | `immer` | 10.x | Used via Zustand's `immer` middleware for nested state mutations. |

See [ADR-007](../adr/007-state-management.md) for the decision record and store slice design.

---

## UI System

| Technology | Package | Version | Notes |
|---|---|---|---|
| Tailwind CSS | `tailwindcss` | 4.x | CSS-first configuration (`@config` directive). No `tailwind.config.js`. |
| shadcn/ui | (CLI-generated components) | current | Components copied into `components/ui/`. Not a package dependency. |
| Radix UI | `@radix-ui/*` | current | Peer dependency of shadcn/ui components. |
| class-variance-authority | `class-variance-authority` | 0.7.x | Used for component variant composition. |
| clsx + tailwind-merge | `clsx`, `tailwind-merge` | current | Used in the `cn()` utility. |

---

## Backend

| Technology | Package | Version | Notes |
|---|---|---|---|
| Next.js Route Handlers | (built-in) | — | All API routes use `app/api/` route handlers. |
| Zod | `zod` | 3.x | Request body validation at all API boundaries. |

No separate backend server process. All server-side logic runs through Next.js.

---

## Database

| Technology | Package | Version | Notes |
|---|---|---|---|
| PostgreSQL | — | 17.x | Primary data store. JSONB used for artifact content fields. |
| Prisma ORM | `prisma`, `@prisma/client` | 6.x | Schema lives at `prisma/schema.prisma`. Migrations tracked in `prisma/migrations/`. |

### Prisma Conventions

- All migrations use `prisma migrate dev` in development, `prisma migrate deploy` in CI/CD
- Schema file: `prisma/schema.prisma`
- Prisma client is instantiated once as a singleton in `lib/prisma.ts`
- Row-level softdelete uses `deletedAt: DateTime?` columns (see `initial-schema.md`)

---

## Real-Time Collaboration

| Technology | Package | Version | Notes |
|---|---|---|---|
| Liveblocks | `@liveblocks/react`, `@liveblocks/client` | 2.x | Multi-user presence and canvas state sync. Used in Phase 8. |

Liveblocks is listed here for version pinning. It is not implemented until Phase 8. The dependency may be installed early but must not be wired into the application before Phase 8.

---

## Background Jobs and Queue

| Technology | Package | Version | Notes |
|---|---|---|---|
| BullMQ | `bullmq` | 5.x | Queue-backed async execution for long-running node operations. |
| Redis | — (server) | 7.x | Backing store for BullMQ. Use Redis 7+ for BullMQ v5 compatibility. |
| ioredis | `ioredis` | 5.x | Redis client used by BullMQ. |

### BullMQ Conventions

- Queue definitions live in `modules/execution/queues/`
- Worker processes are separate files under `modules/execution/workers/`
- Job types are strongly typed using TypeScript generics on `Queue<JobData, JobResult>`

---

## AI Providers

| Technology | Package | Version | Notes |
|---|---|---|---|
| Anthropic SDK | `@anthropic-ai/sdk` | 0.40.x | Claude model access. Default provider. |
| OpenAI SDK | `openai` | 4.x | GPT model access. Secondary provider. |

Both SDKs are used only through the AI abstraction layer defined in [ADR-005](../adr/005-ai-provider-abstraction.md) and [AI Architecture](ai-architecture.md). No module may import these SDKs directly.

---

## Testing

| Technology | Package | Version | Notes |
|---|---|---|---|
| Vitest | `vitest` | 2.x | Unit and integration test runner. Replaces Jest for this project. |
| React Testing Library | `@testing-library/react` | 16.x | Component-level rendering tests. |
| @testing-library/user-event | `@testing-library/user-event` | 14.x | User interaction simulation. |
| Playwright | `@playwright/test` | 1.x | End-to-end testing. E2E tests live in `e2e/`. |
| MSW | `msw` | 2.x | API mocking for integration tests that exercise fetch boundaries. |

### Testing Conventions

- Unit tests: colocated with source files as `*.test.ts` or `*.test.tsx`
- Integration tests: in `__tests__/` subdirectories within each module
- E2E tests: in `e2e/` at the repository root
- Test coverage is not enforced by CI at MVP stage; coverage tooling added in Phase 6+

---

## Tooling and DX

| Technology | Package | Version | Notes |
|---|---|---|---|
| ESLint | `eslint` | 9.x | Flat config format (`eslint.config.mjs`). |
| eslint-config-next | `eslint-config-next` | matching Next.js | Extended by the project ESLint config. |
| Prettier | `prettier` | 3.x | Code formatting. Runs via `prettier --check` in CI. |
| TypeScript paths | (via `tsconfig.json`) | — | Path aliases configured via `paths` in `tsconfig.json`. |
| tsx | `tsx` | 4.x | TypeScript script runner for migrations, seed scripts, and tooling. |

### Path Alias Conventions

```
@/            ->  ./src/        (application source)
@modules/     ->  ./modules/    (domain modules)
@lib/         ->  ./lib/        (shared utilities)
```

---

## Environment and Configuration

| Technology | Pattern | Notes |
|---|---|---|
| Environment variables | `.env` files | `dotenv` loaded by Next.js automatically. `.env.local` for local overrides (gitignored). |
| Type-safe env | `@t3-oss/env-nextjs` | 1.x. Validates environment variables at startup using Zod. Fail-fast on missing required env. |

All secrets live in environment variables. No secrets in code or committed config files.

---

## Dependency Decisions Summary

| Decision | Rejected Alternatives |
|---|---|
| Next.js App Router | Pages Router (legacy), separate Express API |
| React Flow | D3.js, Cytoscape.js, custom canvas (see ADR-006) |
| Zustand | Redux Toolkit, Jotai, React Context (see ADR-007) |
| Prisma | Drizzle, raw pg, TypeORM |
| BullMQ | Agenda, node-cron, inline async processing |
| Vitest | Jest (slower, heavier, less ESM-compatible) |
| pnpm | npm, yarn (slower installs, weaker hoisting control) |
| Tailwind 4 | Tailwind 3, CSS Modules, CSS-in-JS (styled-components) |
