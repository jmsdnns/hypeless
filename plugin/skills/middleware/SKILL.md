---
name: middleware
description: Scaffold Express middleware (auth, validation, logging, etc.)
---

Create a new Express middleware.

## Gather Information

Ask the user for:
1. Middleware type:
   - **auth** - JWT/session authentication
   - **validate** - Request body validation
   - **rateLimit** - Rate limiting
   - **logging** - Request logging
   - **custom** - Custom middleware

2. Any specific requirements based on type

## File Location

Create `src/middleware/{name}.ts`

## Templates by Type

### Auth Middleware

```typescript
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';

export interface AuthRequest extends Request {
  user?: { id: string; email: string };
}

export const authenticate = (
  req: AuthRequest,
  res: Response,
  next: NextFunction
) => {
  const authHeader = req.headers.authorization;

  if (!authHeader?.startsWith('Bearer ')) {
    return res.status(401).json({ error: 'Missing authorization header' });
  }

  const token = authHeader.substring(7);

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as AuthRequest['user'];
    req.user = decoded;
    next();
  } catch {
    return res.status(401).json({ error: 'Invalid token' });
  }
};
```

### Validation Middleware (with zod)

```typescript
import { Request, Response, NextFunction } from 'express';
import { ZodSchema, ZodError } from 'zod';

export const validate = (schema: ZodSchema) => {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      schema.parse(req.body);
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        return res.status(400).json({
          error: 'Validation failed',
          details: error.errors.map(e => ({
            field: e.path.join('.'),
            message: e.message,
          })),
        });
      }
      next(error);
    }
  };
};

// Usage:
// import { z } from 'zod';
// const createUserSchema = z.object({
//   email: z.string().email(),
//   name: z.string().min(1),
// });
// router.post('/users', validate(createUserSchema), controller.create);
```

### Rate Limit Middleware

```typescript
import { Request, Response, NextFunction } from 'express';

interface RateLimitStore {
  [key: string]: { count: number; resetTime: number };
}

const store: RateLimitStore = {};

export const rateLimit = (maxRequests: number, windowMs: number) => {
  return (req: Request, res: Response, next: NextFunction) => {
    const key = req.ip || 'unknown';
    const now = Date.now();

    if (!store[key] || now > store[key].resetTime) {
      store[key] = { count: 1, resetTime: now + windowMs };
      return next();
    }

    if (store[key].count >= maxRequests) {
      return res.status(429).json({ error: 'Too many requests' });
    }

    store[key].count++;
    next();
  };
};

// Usage: router.use(rateLimit(100, 60000)); // 100 requests per minute
```

### Logging Middleware

```typescript
import { Request, Response, NextFunction } from 'express';

export const requestLogger = (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  const start = Date.now();

  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(
      `${req.method} ${req.path} ${res.statusCode} - ${duration}ms`
    );
  });

  next();
};
```

### Custom Middleware Template

```typescript
import { Request, Response, NextFunction } from 'express';

export const {name} = (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  // Middleware logic here

  next();
};
```

## Guidelines

- Always call `next()` to continue the chain (unless sending a response)
- Type the Request if adding custom properties (use interface extension)
- Keep middleware focused on one concern
- Export for use in routes or app-level
