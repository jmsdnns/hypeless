---
name: init-rest
description: Initialize Express + TypeScript + Prisma REST API project
---

Create a new Express REST API with TypeScript and Prisma. Ask the user for a project name first.

## Directory Structure

```
{project-name}/
├── package.json
├── tsconfig.json
├── prisma/
│   └── schema.prisma
└── src/
    ├── index.ts           # Express server setup
    ├── routes/
    │   └── index.ts       # Route aggregator
    ├── controllers/
    │   └── health.ts      # Health check controller
    ├── middleware/
    │   └── errorHandler.ts
    └── types/
        └── index.ts       # Shared types
```

## Dependencies

**Production:**
- express
- cors
- helmet

**Dev:**
- typescript
- ts-node-dev
- @types/express
- @types/cors
- @types/node
- prisma

## Key Files

**package.json** - Include scripts:
- `dev`: ts-node-dev with --respawn for development
- `build`: tsc compilation
- `start`: run compiled code

**src/index.ts** - Express setup with:
- JSON body parsing
- CORS enabled
- Helmet security headers
- Error handling middleware
- Listen on port 3000

**src/routes/index.ts** - Route aggregator pattern:
```typescript
import { Router } from 'express';
import healthRoutes from './health';

const router = Router();
router.use('/health', healthRoutes);

export default router;
```

**src/controllers/health.ts** - Simple health check:
```typescript
import { Request, Response } from 'express';

export const getHealth = (_req: Request, res: Response) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
};
```

**src/middleware/errorHandler.ts** - Centralized error handling:
```typescript
import { Request, Response, NextFunction } from 'express';

export const errorHandler = (
  err: Error,
  _req: Request,
  res: Response,
  _next: NextFunction
) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Internal server error' });
};
```

**src/types/index.ts** - Start with common response types:
```typescript
export interface ApiResponse<T> {
  data?: T;
  error?: string;
  message?: string;
}
```

**prisma/schema.prisma** - SQLite by default:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}
```

**.gitignore** - Include:
```
node_modules/
dist/
*.db
.env
```

## Guidelines

- Use Express 5.x patterns
- Keep it minimal—no auth, no complex middleware
- Type all request handlers properly
- User can build on top of this skeleton
