---
name: integrate
version: 0.1.0
description: Integrate logan-logger into TypeScript apps with correct runtime imports, environment config, safe metadata, and validation
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /logan-logger:integrate

Use this skill when the user wants to add or improve `logan-logger` in a JavaScript or TypeScript project, including Node.js, Next.js, browser, Deno, Bun, API routes, structured logging, safe serialization, or optional Winston transports.

Examples:

- "Add logan-logger to this Next.js app."
- "Replace console.log with structured logger calls."
- "Use logan-logger in Node with file logging."
- "Make browser logging safe for production."
- "Choose the right logan-logger import for Bun/Deno/browser."

## Install

For npm/pnpm/yarn projects:

```bash
pnpm add logan-logger
npm install logan-logger
yarn add logan-logger
```

For Deno/JSR:

```bash
deno add @logan/logger
npx jsr add @logan/logger
```

Add `winston` only when the user needs Node file/transports:

```bash
pnpm add winston
```

## Choose The Import

Use the most specific runtime import that matches the target file:

```typescript
// Generic auto-detection
import { createLogger } from 'logan-logger';

// Node.js, server-only code, optional Winston support
import { createLogger, NodeLogger, createMorganStream } from 'logan-logger/node';

// Browser, client components, workers
import { createLogger, BrowserLogger, PerformanceLogger } from 'logan-logger/browser';

// Bun
import { createLogger, NodeLogger } from 'logan-logger/bun';

// Deno via JSR
import { createLogger } from '@logan/logger/deno';
```

For Next.js:

- Server Components and API routes can use `logan-logger` or `logan-logger/node`.
- Client Components must use `logan-logger/browser` and start with `'use client'`.
- Edge Runtime should avoid Node-only transports.

## Basic Pattern

Create one shared logger per runtime boundary:

```typescript
import { createLogger, LogLevel } from 'logan-logger';

export const logger = createLogger({
  level: process.env.NODE_ENV === 'development' ? LogLevel.DEBUG : LogLevel.INFO,
  format: process.env.NODE_ENV === 'production' ? 'json' : 'text',
  metadata: { service: 'my-app' }
});
```

Use child loggers for request or domain context:

```typescript
const requestLogger = logger.child({
  requestId,
  userId
});

requestLogger.info('Processing request', { endpoint: '/api/users' });
```

## Safe Metadata

Never log raw secrets, tokens, passwords, full auth headers, or private keys.

Use sensitive-data filtering when logging user or request objects:

```typescript
import { filterSensitiveData } from 'logan-logger';

logger.info('User processed', filterSensitiveData(userData));
```

For errors, prefer structured but bounded metadata:

```typescript
logger.error('Operation failed', {
  error: error instanceof Error ? error.message : String(error),
  operation: 'sync'
});
```

## Performance

Use lazy evaluation for expensive debug messages:

```typescript
logger.debug(() => `Expensive value: ${computeHeavyValue()}`);
```

Do not build large metadata objects for logs below the active level.

## Environment Configuration

Common environment variables:

```bash
LOG_LEVEL=debug
LOG_FORMAT=json
LOG_TIMESTAMP=true
LOG_COLOR=false
```

Use `createLoggerForEnvironment()` when the project wants conventional defaults:

```typescript
import { createLoggerForEnvironment } from 'logan-logger';

export const logger = createLoggerForEnvironment();
```

## Migration From console

When replacing `console.*`:

1. Create a shared logger module.
2. Replace `console.debug/info/warn/error` with matching logger methods.
3. Add context metadata instead of interpolating everything into message strings.
4. Keep messages stable and human-readable.
5. Filter sensitive data before logging request bodies or user records.
6. Validate in the runtime where the file executes.

## Validation

Run the target project’s checks:

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

For library/package consumers, verify import paths compile under the project’s bundler. For Next.js, test both server and client files when both use logging.
