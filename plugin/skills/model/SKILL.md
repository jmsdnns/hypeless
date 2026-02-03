---
name: model
description: Add a Prisma model and generate types
---

Add a new model to the Prisma schema and generate TypeScript types.

## Gather Information

Ask the user for:
1. Model name (PascalCase, e.g., `User`, `BlogPost`)
2. Fields with their types

## Common Field Types

| Prisma Type | Use For |
|-------------|---------|
| `String` | Text, emails, names |
| `String?` | Optional text |
| `Int` | Whole numbers |
| `Float` | Decimals (not money) |
| `Decimal` | Money, precise values |
| `Boolean` | True/false |
| `DateTime` | Timestamps |
| `Json` | Flexible data (use sparingly) |

## Template

Add to `prisma/schema.prisma`:

```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([email])
}
```

## Relations

If the user mentions relations to other models:

**One-to-many:**
```prisma
model User {
  id    String @id @default(cuid())
  posts Post[]
}

model Post {
  id       String @id @default(cuid())
  author   User   @relation(fields: [authorId], references: [id])
  authorId String

  @@index([authorId])
}
```

**Many-to-many:**
```prisma
model Post {
  id       String     @id @default(cuid())
  tags     Tag[]
}

model Tag {
  id    String @id @default(cuid())
  posts Post[]
}
```

## After Adding Model

1. Run `npx prisma generate` to update TypeScript types
2. Ask: **"Want me to create a route and controller for this model?"**
   - If yes, proceed as `/route` would for this resource

## Guidelines

- Always include `id`, `createdAt`, `updatedAt` unless user says otherwise
- Use `cuid()` for IDs (shorter than UUID, sortable)
- Add `@@index` on foreign keys and frequently queried fields
- Use `@unique` for emails, slugs, etc.
- Use `Decimal` for money, never `Float`
