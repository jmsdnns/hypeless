---
name: route
description: Scaffold a new REST API route with controller
---

Create a new REST API route with its controller.

## Gather Information

Ask the user for:
1. Resource name (singular, e.g., `user`, `post`, `comment`)
2. Which endpoints to include:
   - GET /resources (list)
   - GET /resources/:id (get one)
   - POST /resources (create)
   - PUT /resources/:id (update)
   - DELETE /resources/:id (delete)

## Files to Create

### Route file: `src/routes/{resource}.ts`

```typescript
import { Router } from 'express';
import * as controller from '../controllers/{resource}';

const router = Router();

router.get('/', controller.list);
router.get('/:id', controller.getById);
router.post('/', controller.create);
router.put('/:id', controller.update);
router.delete('/:id', controller.remove);

export default router;
```

### Controller file: `src/controllers/{resource}.ts`

```typescript
import { Request, Response, NextFunction } from 'express';
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

export const list = async (req: Request, res: Response, next: NextFunction) => {
  try {
    const items = await prisma.{resource}.findMany();
    res.json({ data: items });
  } catch (error) {
    next(error);
  }
};

export const getById = async (req: Request, res: Response, next: NextFunction) => {
  try {
    const { id } = req.params;
    const item = await prisma.{resource}.findUnique({ where: { id } });
    if (!item) {
      return res.status(404).json({ error: 'Not found' });
    }
    res.json({ data: item });
  } catch (error) {
    next(error);
  }
};

export const create = async (req: Request, res: Response, next: NextFunction) => {
  try {
    const item = await prisma.{resource}.create({ data: req.body });
    res.status(201).json({ data: item });
  } catch (error) {
    next(error);
  }
};

export const update = async (req: Request, res: Response, next: NextFunction) => {
  try {
    const { id } = req.params;
    const item = await prisma.{resource}.update({
      where: { id },
      data: req.body,
    });
    res.json({ data: item });
  } catch (error) {
    next(error);
  }
};

export const remove = async (req: Request, res: Response, next: NextFunction) => {
  try {
    const { id } = req.params;
    await prisma.{resource}.delete({ where: { id } });
    res.status(204).send();
  } catch (error) {
    next(error);
  }
};
```

## After Creating Files

1. Add route to `src/routes/index.ts`:
```typescript
import {resource}Routes from './{resource}';
router.use('/{resources}', {resource}Routes);
```

2. Check if Prisma model exists - if not, suggest creating it

## Guidelines

- Use async/await with try-catch
- Pass errors to next() for centralized handling
- Return appropriate HTTP status codes
- Use plural for route paths (`/users`), singular for files (`user.ts`)
- Keep controllers thin—complex logic goes in services
