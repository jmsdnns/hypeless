---
name: service
description: Scaffold a service layer for business logic
---

Create a service to encapsulate business logic separate from controllers.

## Gather Information

Ask the user for:
1. Service name (e.g., `user`, `auth`, `email`)
2. Key operations it should handle

## File Location

Create `src/services/{name}.ts`

## Template

```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

// Types for this service
export interface Create{Name}Input {
  // Define input fields
}

export interface Update{Name}Input {
  // Define update fields
}

// Service class or object
export const {name}Service = {
  async findAll() {
    return prisma.{model}.findMany({
      select: {
        id: true,
        // Add fields to return
      },
    });
  },

  async findById(id: string) {
    const item = await prisma.{model}.findUnique({
      where: { id },
    });

    if (!item) {
      throw new NotFoundError('{Name} not found');
    }

    return item;
  },

  async create(input: Create{Name}Input) {
    // Validate business rules here
    // e.g., check for duplicates, validate relationships

    return prisma.{model}.create({
      data: input,
    });
  },

  async update(id: string, input: Update{Name}Input) {
    // Check exists
    await this.findById(id);

    return prisma.{model}.update({
      where: { id },
      data: input,
    });
  },

  async delete(id: string) {
    // Check exists
    await this.findById(id);

    // Handle related data if needed
    // e.g., soft delete, cascade rules

    return prisma.{model}.delete({
      where: { id },
    });
  },
};
```

## Custom Error Classes

Create `src/errors/index.ts` if it doesn't exist:

```typescript
export class AppError extends Error {
  constructor(
    message: string,
    public statusCode: number = 500,
    public code?: string
  ) {
    super(message);
    this.name = 'AppError';
  }
}

export class NotFoundError extends AppError {
  constructor(message = 'Resource not found') {
    super(message, 404, 'NOT_FOUND');
    this.name = 'NotFoundError';
  }
}

export class ValidationError extends AppError {
  constructor(message: string, public details?: unknown) {
    super(message, 400, 'VALIDATION_ERROR');
    this.name = 'ValidationError';
  }
}

export class UnauthorizedError extends AppError {
  constructor(message = 'Unauthorized') {
    super(message, 401, 'UNAUTHORIZED');
    this.name = 'UnauthorizedError';
  }
}
```

Update error handler to use these:

```typescript
import { Request, Response, NextFunction } from 'express';
import { AppError } from '../errors';

export const errorHandler = (
  err: Error,
  _req: Request,
  res: Response,
  _next: NextFunction
) => {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      error: err.message,
      code: err.code,
    });
  }

  console.error(err.stack);
  res.status(500).json({ error: 'Internal server error' });
};
```

## Using Services in Controllers

```typescript
import { Request, Response, NextFunction } from 'express';
import { userService } from '../services/user';

export const list = async (_req: Request, res: Response, next: NextFunction) => {
  try {
    const users = await userService.findAll();
    res.json({ data: users });
  } catch (error) {
    next(error);
  }
};
```

## Guidelines

- Services contain business logic, controllers handle HTTP
- Throw typed errors, let error middleware handle responses
- Use Prisma `select` to limit returned fields
- Validate business rules before database operations
- Keep services testable—inject prisma if needed for mocking
