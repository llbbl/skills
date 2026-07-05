---
name: launch-docs
version: 0.1.0
description: Launch or adapt a semantic-docs site with Astro content, semantic search indexing, Turso or local libSQL, and deployment checks
allowed-tools: Bash, Read, Grep, Glob, Edit, MultiEdit, Write
---

# /semantic-docs:launch-docs

Use this skill when the user wants to adopt `semantic-docs`, build a searchable documentation site, migrate markdown content into the theme, configure semantic search, or prepare the site for deployment.

Examples:

- "Set up semantic-docs for this project."
- "Turn these markdown docs into a searchable docs site."
- "Configure Turso search for semantic-docs."
- "Debug why semantic-docs search is empty."
- "Prepare this semantic-docs site for deployment."

## Project Model

`semantic-docs` is an Astro documentation theme with server-rendered semantic search powered by `libsql-search`.

Core pieces:

- content lives under `content/`
- article routes render from `src/pages/content/[...slug].astro`
- the layout is `src/layouts/DocsLayout.astro`
- search UI lives in `src/components/Search.tsx`
- search API lives in `src/pages/api/search.json.ts`
- indexing is handled by `scripts/index-content.ts`
- database setup is handled by `scripts/init-db.ts`

Use local indexing first when credentials are not available, then move to Turso for deployment.

## Setup Workflow

1. Inspect the target docs content and current framework.
2. Install dependencies:

```bash
pnpm install
```

3. For local search without Turso credentials:

```bash
pnpm db:init:local
pnpm index:local
pnpm dev
```

4. For Turso-backed search, copy `.env.example` to `.env` and set:

```env
TURSO_DB_URL=libsql://your-database.turso.io
TURSO_AUTH_TOKEN=your-auth-token
```

Then run:

```bash
pnpm db:init
pnpm index
pnpm dev
```

## Content Migration

Place markdown under `content/` and preserve a clean information architecture:

```text
content/
  getting-started/
    intro.md
  guides/
    configuration.md
  reference/
    api.md
```

Use front matter where helpful:

```markdown
---
title: Getting Started
tags: [tutorial, beginner]
---
```

When migrating existing docs:

1. Keep one clear H1 per page.
2. Preserve stable URLs when users may have links to old docs.
3. Group content by user workflow, not by internal source-code layout.
4. Add tags for search result context.
5. Run indexing after content changes.

## Customization

For site identity:

- update the site title in `src/components/DocsHeader.astro`
- update default title/description in `src/layouts/DocsLayout.astro`

For theme styling:

- edit `src/styles/global.css`
- keep OKLCH color choices coherent and accessible
- check mobile sidebar, search dialog, and article readability

For content behavior:

- adjust markdown rendering in `src/lib/markdown.ts`
- keep sanitization in place when rendering user-authored or imported markdown

## Search Debugging

If search returns no useful results:

1. Confirm the database is initialized.
2. Confirm content was indexed after markdown changes.
3. Check that the Astro app is running with `output: 'server'`.
4. Verify `.env` values for Turso deployments.
5. Try local commands to isolate credential issues:

```bash
pnpm db:init:local
pnpm index:local
pnpm dev
```

The first local embedding run may download a model and be slower than later runs.

## Deployment

Prefer container-capable platforms such as Railway, Render, Fly.io, Google Cloud Run, AWS ECS/Fargate, Azure Container Apps, or Coolify.

Before deploying:

```bash
pnpm index
pnpm build
```

Ensure deployment environment variables include:

- `TURSO_DB_URL`
- `TURSO_AUTH_TOKEN`

Do not recommend static-only hosting unless the user understands that server-rendered search needs a runtime.

## Validation

Use the project’s Just recipes when available:

```bash
just check
just db-init-local
just index-local
just build
```

Or use package scripts directly:

```bash
pnpm lint
pnpm exec tsc --noEmit
pnpm test --run
pnpm db:init:local
pnpm index:local
pnpm build
```

When changing search or indexing, validate both local indexing and the search API behavior.
