---
description: 'Best practices for defining Payload Collections and Schema.'
applyTo: '/src/payload/collections/**/*.ts, /src/payload/blocks/**/*.ts'
---

# Payload Collection Definitions

You are defining the content schema for Payload CMS.

## Configuration Rules

### Slugs
- Collection slugs should be singular and camelCase or kebab-case (e.g., `caseStudies`, `blog-posts`).
- Consistent naming prevents confusion in API routes (`/api/caseStudies`).

### TypeScript Interfaces
- Do not manually define interfaces for your collections.
- Rely on `payload generate:types` to create the Config interface.
- Use `CollectionConfig` type for the export.

## Field Best Practices

### Relationships
- Always define `relationTo`. If polymorphic, use an array `relationTo: ['posts', 'events']`.

### Selects
- Use `hasMany: true` if multiple options are allowed.
- Provide `value` and `label` for options.

### Uploads
- Define `upload: true` or strict configuration (`mimeTypes`, `imageSizes`) on the Collection itself, not just the field.

### Hooks
- Keep hook logic in separate files if they grow larger than 10 lines.
- `import { beforeChangeHook } from './hooks/beforeChange'`

## Example Structure

```typescript
import { CollectionConfig } from 'payload'

export const Media: CollectionConfig = {
  slug: 'media',
  upload: {
    staticDir: 'media',
    imageSizes: [
      {
        name: 'thumbnail',
        width: 400,
        height: 300,
        position: 'centre',
      },
    ],
    mimeTypes: ['image/*'],
  },
  fields: [
    {
      name: 'alt',
      type: 'text',
      required: true,
    },
  ],
}
```
