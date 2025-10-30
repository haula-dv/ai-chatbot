# Development Guide

This guide covers development workflows, best practices, and common patterns for working with this codebase.

## Table of Contents

1. [Getting Started](#getting-started)
2. [Project Structure](#project-structure)
3. [Development Workflow](#development-workflow)
4. [Code Patterns](#code-patterns)
5. [API Integration](#api-integration)
6. [Database Operations](#database-operations)
7. [Testing](#testing)
8. [Deployment](#deployment)
9. [Troubleshooting](#troubleshooting)

---

## Getting Started

### Prerequisites

- Node.js 18+ (LTS recommended)
- pnpm, npm, or yarn
- PostgreSQL database
- Redis (optional, for resumable streams)

### Installation

```bash
# Clone the repository
git clone <repository-url>
cd <project-directory>

# Install dependencies
pnpm install

# Set up environment variables
cp .env.example .env.local
# Edit .env.local with your configuration

# Run database migrations
pnpm db:migrate

# Start development server
pnpm dev
```

### Environment Setup

Create `.env.local` with the following variables:

```env
# Database
POSTGRES_URL=postgresql://user:password@localhost:5432/dbname

# Authentication
AUTH_SECRET=generate-a-random-secret-here
NEXTAUTH_URL=http://localhost:3000

# Redis (optional)
REDIS_URL=redis://localhost:6379

# AI Provider Configuration
# Add your AI provider credentials here
```

### Database Setup

```bash
# Run migrations
pnpm db:migrate

# Seed database (optional)
pnpm db:seed

# Open Drizzle Studio
pnpm db:studio
```

---

## Project Structure

```
/workspace/
├── app/                    # Next.js app directory
│   ├── (auth)/            # Authentication routes & logic
│   │   ├── auth.ts        # NextAuth configuration
│   │   ├── actions.ts     # Server actions (login, register)
│   │   └── api/           # Auth API routes
│   ├── (chat)/            # Chat routes & logic
│   │   ├── actions.ts     # Chat server actions
│   │   ├── api/           # Chat API routes
│   │   └── chat/          # Chat pages
│   ├── globals.css        # Global styles
│   └── layout.tsx         # Root layout
│
├── artifacts/             # Artifact handlers
│   ├── code/             # Code artifact
│   ├── image/            # Image artifact
│   ├── sheet/            # Sheet artifact
│   └── text/             # Text artifact
│
├── components/            # React components
│   ├── ui/               # Base UI components (Radix)
│   ├── elements/         # Message elements
│   ├── chat.tsx          # Main chat component
│   ├── artifact.tsx      # Artifact component
│   └── ...
│
├── hooks/                # Custom React hooks
│   ├── use-artifact.ts   # Artifact state management
│   ├── use-messages.tsx  # Message list management
│   └── ...
│
├── lib/                  # Utilities and libraries
│   ├── ai/              # AI provider integration
│   │   ├── models.ts    # Model configurations
│   │   ├── prompts.ts   # System prompts
│   │   ├── providers.ts # AI provider setup
│   │   └── tools/       # AI tools
│   ├── db/              # Database layer
│   │   ├── queries.ts   # Database queries
│   │   ├── schema.ts    # Drizzle schema
│   │   └── migrations/  # SQL migrations
│   ├── types.ts         # TypeScript types
│   ├── utils.ts         # Utility functions
│   └── errors.ts        # Error handling
│
├── tests/               # Test files
│   ├── e2e/            # End-to-end tests
│   ├── pages/          # Page object models
│   └── prompts/        # Test prompts
│
└── public/             # Static assets
```

---

## Development Workflow

### Creating a New Feature

1. **Create a new branch**
```bash
git checkout -b feature/my-feature
```

2. **Implement the feature**
   - Add types in `lib/types.ts`
   - Create database schema if needed
   - Implement API routes
   - Create components
   - Add tests

3. **Test locally**
```bash
pnpm test
pnpm lint
```

4. **Commit and push**
```bash
git add .
git commit -m "feat: add my feature"
git push origin feature/my-feature
```

### Hot Reload

The development server supports hot module replacement (HMR):

```bash
pnpm dev
```

Changes to files will automatically reload in the browser.

### Code Quality

Run linting and formatting:

```bash
# Lint
pnpm lint

# Format with Biome
pnpm format

# Type check
pnpm type-check
```

---

## Code Patterns

### Server Actions

Server actions are used for mutations:

```typescript
// app/actions.ts
'use server';

import { auth } from '@/app/(auth)/auth';
import { revalidatePath } from 'next/cache';

export async function updateProfile(formData: FormData) {
  const session = await auth();
  
  if (!session?.user) {
    throw new Error('Unauthorized');
  }
  
  // Update user profile
  await db.update(user)
    .set({ name: formData.get('name') })
    .where(eq(user.id, session.user.id));
  
  // Revalidate cache
  revalidatePath('/profile');
  
  return { success: true };
}
```

**Usage in components:**
```tsx
'use client';

import { updateProfile } from './actions';

export function ProfileForm() {
  return (
    <form action={updateProfile}>
      <input name="name" />
      <button type="submit">Save</button>
    </form>
  );
}
```

### API Routes

API routes handle HTTP requests:

```typescript
// app/api/resource/route.ts
import { auth } from '@/app/(auth)/auth';
import { NextRequest } from 'next/server';

export async function GET(request: NextRequest) {
  const session = await auth();
  
  if (!session?.user) {
    return Response.json({ error: 'Unauthorized' }, { status: 401 });
  }
  
  const data = await fetchData();
  
  return Response.json(data);
}

export async function POST(request: NextRequest) {
  const body = await request.json();
  
  // Validate
  const validated = schema.parse(body);
  
  // Process
  const result = await processData(validated);
  
  return Response.json(result);
}
```

### Database Queries

Always use prepared statements with Drizzle:

```typescript
// lib/db/queries.ts
import { db } from './index';
import { chat, message } from './schema';

export async function getChatWithMessages(chatId: string) {
  const [chatData] = await db
    .select()
    .from(chat)
    .where(eq(chat.id, chatId));
  
  if (!chatData) {
    throw new ChatSDKError('not_found:chat');
  }
  
  const messages = await db
    .select()
    .from(message)
    .where(eq(message.chatId, chatId))
    .orderBy(asc(message.createdAt));
  
  return { chat: chatData, messages };
}
```

### React Hooks

Custom hooks for reusable logic:

```typescript
// hooks/use-feature.ts
import { useState, useEffect } from 'react';

export function useFeature() {
  const [state, setState] = useState(initialState);
  
  useEffect(() => {
    // Side effect
    const subscription = subscribe();
    return () => subscription.unsubscribe();
  }, []);
  
  const action = () => {
    setState(newState);
  };
  
  return { state, action };
}
```

**Usage:**
```tsx
function Component() {
  const { state, action } = useFeature();
  
  return <button onClick={action}>{state}</button>;
}
```

### Error Handling

Use the custom error class:

```typescript
import { ChatSDKError } from '@/lib/errors';

// Throw errors
throw new ChatSDKError('not_found:chat', 'Chat not found');

// Catch and handle
try {
  await riskyOperation();
} catch (error) {
  if (error instanceof ChatSDKError) {
    return error.toResponse();
  }
  throw error;
}
```

---

## API Integration

### Creating a New API Endpoint

1. **Define the schema**
```typescript
// app/api/resource/schema.ts
import { z } from 'zod';

export const requestSchema = z.object({
  name: z.string().min(1).max(100),
  value: z.number().positive(),
});

export type Request = z.infer<typeof requestSchema>;
```

2. **Implement the route**
```typescript
// app/api/resource/route.ts
import { auth } from '@/app/(auth)/auth';
import { requestSchema } from './schema';
import { ChatSDKError } from '@/lib/errors';

export async function POST(request: Request) {
  // Authenticate
  const session = await auth();
  if (!session?.user) {
    return new ChatSDKError('unauthorized:api').toResponse();
  }
  
  // Parse and validate
  const body = await request.json();
  const validated = requestSchema.parse(body);
  
  // Process
  const result = await processRequest(validated);
  
  return Response.json(result);
}
```

3. **Add client-side fetch**
```typescript
// lib/api/client.ts
import { fetchWithErrorHandlers } from '@/lib/utils';

export async function createResource(data: Request) {
  const response = await fetchWithErrorHandlers('/api/resource', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
  });
  
  return response.json();
}
```

### Streaming Responses

For Server-Sent Events (SSE):

```typescript
// app/api/stream/route.ts
export async function POST(request: Request) {
  const stream = new ReadableStream({
    async start(controller) {
      // Send data
      controller.enqueue(encoder.encode('data: message\n\n'));
      
      // Close stream
      controller.close();
    },
  });
  
  return new Response(stream, {
    headers: {
      'Content-Type': 'text/event-stream',
      'Cache-Control': 'no-cache',
      'Connection': 'keep-alive',
    },
  });
}
```

**Client usage:**
```typescript
const eventSource = new EventSource('/api/stream');

eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log(data);
};

eventSource.onerror = () => {
  eventSource.close();
};
```

---

## Database Operations

### Creating a Migration

```bash
# Generate migration from schema changes
pnpm db:generate

# Apply migrations
pnpm db:migrate
```

### Adding a New Table

1. **Define schema**
```typescript
// lib/db/schema.ts
export const newTable = pgTable('new_table', {
  id: varchar('id', { length: 64 }).primaryKey(),
  userId: varchar('user_id', { length: 64 })
    .notNull()
    .references(() => user.id, { onDelete: 'cascade' }),
  data: text('data').notNull(),
  createdAt: timestamp('created_at').notNull(),
});

export type NewTable = typeof newTable.$inferSelect;
```

2. **Generate migration**
```bash
pnpm db:generate
```

3. **Add queries**
```typescript
// lib/db/queries.ts
export async function createNewRecord(data: {
  id: string;
  userId: string;
  data: string;
}) {
  try {
    return await db.insert(newTable).values({
      ...data,
      createdAt: new Date(),
    });
  } catch (error) {
    throw new ChatSDKError('bad_request:database', 'Failed to create record');
  }
}
```

### Query Patterns

**Select with conditions:**
```typescript
const results = await db
  .select()
  .from(table)
  .where(and(
    eq(table.userId, userId),
    gte(table.createdAt, startDate)
  ))
  .orderBy(desc(table.createdAt))
  .limit(10);
```

**Join tables:**
```typescript
const results = await db
  .select()
  .from(chat)
  .innerJoin(message, eq(message.chatId, chat.id))
  .where(eq(chat.userId, userId));
```

**Update:**
```typescript
await db
  .update(table)
  .set({ field: newValue })
  .where(eq(table.id, id));
```

**Delete:**
```typescript
await db
  .delete(table)
  .where(eq(table.id, id));
```

---

## Testing

### Unit Tests

```typescript
// lib/utils.test.ts
import { describe, test, expect } from 'vitest';
import { generateUUID } from './utils';

describe('generateUUID', () => {
  test('generates valid UUID', () => {
    const uuid = generateUUID();
    expect(uuid).toMatch(/^[0-9a-f]{8}-[0-9a-f]{4}-4[0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$/i);
  });
});
```

### Integration Tests

```typescript
// tests/api/chat.test.ts
import { describe, test, expect } from 'vitest';
import { testClient } from '../helpers';

describe('POST /api/chat', () => {
  test('creates new chat', async () => {
    const response = await testClient.post('/api/chat', {
      id: 'test-id',
      message: {
        id: 'msg-id',
        role: 'user',
        parts: [{ type: 'text', text: 'Hello' }]
      },
      selectedChatModel: 'chat-model',
      selectedVisibilityType: 'private',
    });
    
    expect(response.status).toBe(200);
  });
});
```

### E2E Tests

```typescript
// tests/e2e/chat.spec.ts
import { test, expect } from '@playwright/test';

test('sends a message', async ({ page }) => {
  await page.goto('/chat');
  
  // Type message
  await page.fill('[data-testid="multimodal-input"]', 'Hello');
  
  // Submit
  await page.click('[data-testid="submit-button"]');
  
  // Wait for response
  await expect(page.locator('[role="assistant"]')).toBeVisible();
});
```

### Running Tests

```bash
# All tests
pnpm test

# Watch mode
pnpm test:watch

# Coverage
pnpm test:coverage

# E2E tests
pnpm test:e2e

# Specific file
pnpm test path/to/test.ts
```

---

## Deployment

### Build for Production

```bash
# Build
pnpm build

# Start production server
pnpm start
```

### Environment Variables

Ensure all required environment variables are set in production:

```env
POSTGRES_URL=...
AUTH_SECRET=...
REDIS_URL=...
# Add others as needed
```

### Database Migrations

Run migrations before deploying:

```bash
pnpm db:migrate
```

### Vercel Deployment

The application is optimized for Vercel:

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Production deployment
vercel --prod
```

**Configuration:**
- Set environment variables in Vercel dashboard
- Configure Postgres database
- Enable Redis (optional)

### Docker Deployment

```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

**Build and run:**
```bash
docker build -t my-app .
docker run -p 3000:3000 my-app
```

---

## Troubleshooting

### Common Issues

#### 1. Database Connection Failed

**Error:** `Error: Failed to connect to database`

**Solution:**
- Check `POSTGRES_URL` in `.env.local`
- Ensure PostgreSQL is running
- Verify credentials

```bash
# Test connection
psql $POSTGRES_URL
```

#### 2. Authentication Not Working

**Error:** `401 Unauthorized`

**Solution:**
- Check `AUTH_SECRET` is set
- Clear cookies and try again
- Verify session configuration

```typescript
// Check session
import { auth } from '@/app/(auth)/auth';
const session = await auth();
console.log(session);
```

#### 3. Streaming Not Resuming

**Error:** Stream stops and doesn't resume

**Solution:**
- Ensure `REDIS_URL` is set
- Check Redis connection
- Verify stream IDs are saved

```bash
# Check Redis
redis-cli ping
```

#### 4. Build Errors

**Error:** `Type error: Cannot find module`

**Solution:**
- Clear `.next` directory
- Reinstall dependencies
- Check TypeScript version

```bash
rm -rf .next
pnpm install
pnpm build
```

#### 5. Rate Limit Errors

**Error:** `Rate limit exceeded`

**Solution:**
- Check `entitlements.ts` configuration
- Verify user type
- Wait for rate limit window to reset

### Debug Mode

Enable debug logging:

```typescript
// lib/debug.ts
export const DEBUG = process.env.NODE_ENV === 'development';

export function debug(...args: any[]) {
  if (DEBUG) {
    console.log('[DEBUG]', ...args);
  }
}
```

**Usage:**
```typescript
import { debug } from '@/lib/debug';

debug('Message sent:', message);
```

### Performance Profiling

Use React DevTools Profiler:

```tsx
import { Profiler } from 'react';

<Profiler id="Chat" onRender={onRenderCallback}>
  <Chat {...props} />
</Profiler>
```

---

## Best Practices

### Security

1. **Always validate user input**
```typescript
const validated = schema.parse(input);
```

2. **Check authentication**
```typescript
const session = await auth();
if (!session?.user) {
  return Response.json({ error: 'Unauthorized' }, { status: 401 });
}
```

3. **Sanitize output**
```typescript
import DOMPurify from 'dompurify';
const clean = DOMPurify.sanitize(userInput);
```

4. **Use environment variables**
```typescript
// ❌ Never hardcode secrets
const apiKey = 'sk-1234567890';

// ✅ Use environment variables
const apiKey = process.env.API_KEY;
```

### Performance

1. **Memoize expensive computations**
```typescript
const result = useMemo(() => expensiveOperation(), [deps]);
```

2. **Lazy load components**
```typescript
const HeavyComponent = lazy(() => import('./HeavyComponent'));
```

3. **Use proper caching**
```typescript
// Next.js cache
export const revalidate = 3600; // 1 hour
```

4. **Optimize images**
```tsx
import Image from 'next/image';

<Image
  src="/image.jpg"
  alt="..."
  width={800}
  height={600}
  loading="lazy"
/>
```

### Code Quality

1. **Write descriptive names**
```typescript
// ❌ Bad
const x = getData();

// ✅ Good
const userProfile = getUserProfile();
```

2. **Keep functions small**
```typescript
// ❌ Bad: 100+ line function
function doEverything() { /* ... */ }

// ✅ Good: Small, focused functions
function fetchData() { /* ... */ }
function transformData() { /* ... */ }
function renderData() { /* ... */ }
```

3. **Use TypeScript strictly**
```typescript
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "noImplicitReturns": true
  }
}
```

4. **Document complex logic**
```typescript
/**
 * Calculates the user's rate limit based on their subscription tier.
 * 
 * @param userId - The user's unique identifier
 * @param tier - The subscription tier ('free' | 'premium')
 * @returns The number of requests allowed in the time window
 */
function calculateRateLimit(userId: string, tier: string): number {
  // Implementation
}
```

---

## Resources

### Documentation

- [Next.js Docs](https://nextjs.org/docs)
- [React Docs](https://react.dev)
- [Drizzle ORM](https://orm.drizzle.team)
- [Tailwind CSS](https://tailwindcss.com/docs)

### Tools

- [Vercel](https://vercel.com) - Deployment
- [GitHub](https://github.com) - Version control
- [Biome](https://biomejs.dev) - Linting and formatting
- [Playwright](https://playwright.dev) - E2E testing

### Community

- Stack Overflow
- GitHub Issues
- Discord/Slack channels

---

**Last Updated:** 2025-10-30
