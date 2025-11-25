---
description: 'Core architecture principles and best practices for Payload CMS v3.0+'
applyTo: '**/payload.config.ts, /src/payload/collections/**/*.ts, /src/payload/globals/**/*.ts, /src/payload/blocks/**/*.ts'
---

# Payload CMS Best Practices

Use these instructions when generating code or architecture for Payload CMS (v3.0+). Payload is a code-first, config-based Headless CMS that integrates deeply with Next.js.

## Core Architecture Principles

### Config-First & Type-Safe
- **Central Config**: Everything starts in `payload.config.ts`.
- **Secret Required**: Ensure the `secret` property is defined in `payload.config.ts` (e.g., `secret: process.env.PAYLOAD_SECRET`).
- **Generated Types**: Always rely on generated types. Run `payload generate:types` frequently.
- **Type Imports**: Use the Config type generic in your code: `import { Payload } from 'payload'`.

### Project Structure (Next.js App Router)
- **Payload Files**: Keep Payload files in `src/payload`.
- **Modular Configs**: Separate configs: `src/payload/collections`, `src/payload/globals`, `src/payload/blocks`.
- **Admin UI**: Keep the Admin UI code (custom components) separate from the API logic if possible.
- **Route Group**: Payload routes live in `src/app/(payload)`.
- **Admin Page**: Standard path: `src/app/(payload)/admin/[[...segments]]/page.tsx`.

## Collection & Field Design

### Modular Configs
- Never put all collections in one file. Export each `CollectionConfig` from its own file.
- Example: `export const Users: CollectionConfig = { ... }`.

### Block-Based Layouts
- Avoid fixed schemas for pages. Use a layout field with type `blocks`.
- Define blocks in `src/payload/blocks/*.ts`.
- Reuse blocks across collections (e.g., Pages, Posts, CaseStudies).

### Hooks Nuances
- **beforeChange**: Use for data validation or transformation before saving.
- **afterChange**: Use for side effects (sending emails, triggering webhooks, revalidating Next.js cache).
- **Context**: Use `req.context` to prevent infinite loops if a hook triggers another update.

## Integration & Interworkings

### Local API (Server-Side)
- Inside Next.js Server Components or API Routes, **never** use `fetch` to call your own Payload REST API.
- Always use the Local API: `await payload.find({ ... })`.
- This bypasses HTTP overhead and serializes data directly.
- **Initialization**: Use `getPayload({ config })` to get the payload instance.

### Rich Text (Lexical)
- Payload v3 uses Lexical by default.
- Do not store HTML in the DB. Store the JSON state.
- Use `@payloadcms/richtext-lexical/react` to render serialized JSON on the frontend.

### Revalidation
- Use `revalidatePath` or `revalidateTag` in `afterChange` hooks to update Next.js ISR cache.
- Example:
  ```typescript
  const afterChangeHook: CollectionAfterChangeHook = ({ doc, req: { payload } }) => {
    payload.logger.info(`Revalidating path: /${doc.slug}`)
    revalidatePath(`/${doc.slug}`)
  }
  ```

## Custom Components (Admin UI)

### Client vs Server
- Payload Admin is a React SPA (Single Page App) inside Next.js.
- Custom fields/views must often be Client Components (`"use client"`).
- Import valid Payload types/components from `@payloadcms/ui`.

### Tailwind CSS
- Payload uses its own design system but supports Tailwind.
- Ensure your custom components don't conflict with Payload's internal styles.

### Environment Variables
- Client-side environment variables must be prefixed with `NEXT_PUBLIC_` to be accessible in the Admin UI.

## Dependencies
- **Sharp**: Ensure `sharp` is installed and configured in `payload.config.ts` if using image resizing.
- **Next.js Plugin**: Ensure `withPayload` wraps your `next.config.js`.
