# Hypeless

![hypeless](docs/images/hypeless.jpg)

A Claude Code plugin for building REST APIs with Express + TypeScript + Prisma.

No hype, just a clean stack.

## Installation

```bash
claude plugin marketplace add jmsdnns/hypeless
claude plugin install hypeless
```

## Skills

| Skill | Description |
|-------|-------------|
| `/init-rest` | Bootstrap Express + TypeScript + Prisma project |
| `/model` | Add Prisma model and generate types |
| `/route` | Scaffold REST endpoints with controller |
| `/middleware` | Create auth, validation, or logging middleware |
| `/service` | Extract business logic to service layer |
| `/review` | Code review using specialist agents |

## Agents

| Agent | Expertise |
|-------|-----------|
| `express-pro` | Routing, middleware, security, error handling |
| `typescript-pro` | Type system, generics, strict mode |
| `prisma-pro` | Schema design, migrations, query optimization |
| `stack-orchestrator` | Routes questions to specialists |
| `task-distributor` | Breaks features into tasks |

## Example

```
/init-rest
```

Creates:
```
my-api/
├── src/
│   ├── index.ts
│   ├── routes/
│   ├── controllers/
│   ├── middleware/
│   └── types/
├── prisma/schema.prisma
├── package.json
└── tsconfig.json
```

Then scaffold features:
```
/model        → Add database model
/route        → Add CRUD endpoints
/middleware   → Add auth/validation
/service      → Extract business logic
```

## Stack

| Tech | Version |
|------|---------|
| Express | 5.x |
| TypeScript | 5.x |
| Prisma | 7.x |

## License

MIT
