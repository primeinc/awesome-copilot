---
description: 'Guidelines for Payload CMS database management, migrations, and seeding.'
applyTo: '/migrations/**/*.ts, **/scripts/seed.ts, **/src/payload/payload.config.ts'
---

# Payload CMS Database Administration

Use these instructions for tasks related to database management, migrations, seeding, and performance tuning in Payload CMS.

## Database Adapters

Payload supports multiple adapters. Determine which one is in use before generating code.

### PostgreSQL (@payloadcms/db-postgres)
- Uses Drizzle ORM under the hood.
- Supports strictly typed schemas.
- Requires migration workflows for production schema changes.
- **Nuance**: In dev, `push: true` enables auto-sync. In prod, strictly use `payload migrate`.

### MongoDB (@payloadcms/db-mongodb)
- Uses Mongoose.
- Schema-less flexibility (great for complex, nested blocks).
- Migrations are supported but optional (used for data transformation, not schema definition).

## Migration Workflow

### Generating Migrations
- **Command**: `payload migrate:create [name]`.
- **Postgres**: Payload auto-generates SQL based on config changes.
- **MongoDB**: You must write the `up` and `down` logic manually using the Local API or direct DB access.

### Migration Structure
- Always export `up` and `down` async functions.
- Access payload via `args.payload`.
- **CRITICAL**: If modifying data in a migration, ensure you don't trigger hooks that might fail due to the schema being in flux. Pass `context: { preventHooks: true }` if necessary.

## Seeding & Data Management

### Seeding Scripts
- Create a standalone script (e.g., `src/scripts/seed.ts`).
- **Initialization**: Use `getPayload` with your config.
  ```typescript
  import { getPayload } from 'payload'
  import configPromise from '@payload-config'

  const payload = await getPayload({ config: configPromise })
  ```
- Check if data exists before creating to make the seed idempotent.

### Relationships
- When seeding relational data, create the "leaf" nodes first (e.g., create Categories before Posts).
- Use the returned `id` from the creation to populate the relationship field.

## Performance Tuning

### Indexing
- Add `index: true` to any field frequently queried in `where` clauses.
- **Compound indexes**: Use `db.id` or adapter-specific configurations to create compound indexes on collections.

### Versions & Drafts
- Heavy usage of versions (drafts) bloats the database.
- **Admin Task**: Create a cron job or script to prune old versions.
  ```typescript
  // Pseudo-code for cleanup
  await payload.delete({
    collection: 'pages',
    where: { _status: { equals: 'draft' }, updatedAt: { less_than: '...' } }
  })
  ```
