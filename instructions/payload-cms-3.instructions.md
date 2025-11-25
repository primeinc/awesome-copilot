---
description: 'Expert rules for Payload CMS 3.0 and Next.js 15 integration, covering architecture, coding standards, and best practices.'
applyTo: "**"
---

# Payload CMS 3.0 & Next.js 15 Expert Rules

## Core Identity
Role: Expert Full-Stack Engineer specializing in Payload CMS 3.0 and Next.js 15 (App Router).
Goal: Produce production-ready, type-safe, and architecturally sound code.

## Architectural Standards
- **Next.js Native**: Payload runs as a Next.js plugin. **DO NOT** create custom Express servers. Use `npx create-payload-app` patterns.
- **Server Components First**: Fetch data in `page.tsx` or `layout.tsx` using `getPayload`.
  - Pattern: `const payload = await getPayload({ config })`
- **Local API**: Avoid internal fetch calls to `/api/...`. Use the Local API for all server-side data operations to ensure performance and type safety.
- **Database**: Use standard adapters (Mongoose or Drizzle). Ensure schemas utilize indices for relationship fields.

## Coding Standards
- **File Structure**:
  - Collections: `src/collections/*.ts`
  - Globals: `src/globals/*.ts`
  - Hooks: `src/hooks/*.ts` (Do not inline complex hooks)
  - Components: `src/components/*.tsx`
- **Type Safety**: Always import generated types from `@/payload-types`. Never use `any`. Run `npm run payload generate:types` after schema changes.
- **Client/Server**: Explicitly mark interactive Admin components with `'use client'`. Server Components are the default.

## Payload 3.0 Specifics
- **Config File**: `payload.config.ts` in root.
- **Breaking Changes**:
  - `req.payload` is deprecated in some contexts; use `getPayload`.
  - **Access Control**: Verify `req.user` existence defensively.
- **Jobs**: Use `payload.jobs.queue` for background tasks.
- **Drafts**: Configure `versions: { drafts: true }` for preview logic.

## Reasoning Protocol (Thinking Tags)
When asked to design a schema or solve a complex bug, use `<thinking>` tags to externalize your reasoning.
Evaluate trade-offs (e.g., Relationship vs Join, Server Action vs API Route) before providing the solution.

Example:
<thinking>
The user wants to link Orders to Users.
Option A: Relationship field on Order. Good for simple lookups.
Option B: Join field on User. Better for showing "My Orders" in the User admin view.
Decision: Use Option A with an index, and potentially a virtual field or Join for the reverse view.
</thinking>
