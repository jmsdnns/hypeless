# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Hypeless is a Claude Code plugin for building REST APIs with Express + TypeScript + Prisma. It provides skills (scaffolding commands) and specialist agents, not a standalone application.

## Plugin Structure

```
hypeless/
├── .claude-plugin/
│   └── marketplace.json        # Marketplace definition
├── plugin/
│   ├── .claude-plugin/
│   │   └── plugin.json         # Plugin metadata
│   ├── agents/                 # Specialist AI agents
│   ├── skills/                 # Scaffolding commands
│   └── hooks.json              # TypeScript validation hook
├── docs/
├── README.md
└── CLAUDE.md
```

## Skills Usage

Invoke skills with `/skill-name` or `/hypeless:skill-name`:

- `/init-rest` - Create new Express + TypeScript + Prisma project
- `/model` - Add Prisma model, then optionally scaffold routes
- `/route` - CRUD endpoints with controller
- `/middleware` - Auth, validation, rate limiting
- `/service` - Business logic layer
- `/review` - Coordinate review with all specialists

## Generated Project Structure

After `/init-rest`, projects follow this layout:

```
{project}/
├── src/
│   ├── index.ts           # Express server entry (port 3000)
│   ├── routes/index.ts    # Route aggregator
│   ├── controllers/       # Request handlers
│   ├── middleware/        # errorHandler.ts included
│   ├── services/          # Business logic
│   └── types/             # Shared types
├── prisma/schema.prisma   # SQLite by default
├── package.json
└── tsconfig.json
```

## Commands for Generated Projects

```bash
npm run dev              # ts-node-dev with --respawn
npm run build            # TypeScript compilation
npm run start            # Run compiled dist/
npx prisma generate      # Generate types from schema
npx prisma migrate dev   # Create/apply migrations
npx prisma db push       # Push schema (prototyping only)
```

## Stack Conventions

**Prisma Models:**
- Always include `id` (cuid), `createdAt`, `updatedAt`
- Use `@unique` for emails, slugs
- Add `@@index` on foreign keys and queried fields
- Use `Decimal` for money, never `Float`

**Routing:**
- Plural paths (`/users`), singular files (`user.ts`)
- Route aggregation in `src/routes/index.ts`
- Controllers handle HTTP only; services hold logic

**Middleware Order:**
1. helmet → 2. cors → 3. express.json → 4. routes → 5. errorHandler (last)

**Error Handling:**
- Express 5.x native async handling
- Pass errors to `next()` for centralized middleware
- Throw typed errors; middleware handles responses

## Hook Behavior

After every `Edit` or `Write` in a TypeScript project, the hook runs:
```bash
npx tsc --noEmit
```
Only executes if `tsconfig.json` exists in the working directory.

## Agents

Route complex questions to specialists:
- `express-pro` - Middleware, routing, security, async errors
- `typescript-pro` - Type system, generics, strict mode
- `prisma-pro` - Schema design, relations, query optimization
- `stack-orchestrator` - Routes to the right specialist
- `task-distributor` - Breaks features into tasks
