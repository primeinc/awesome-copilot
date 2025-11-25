---
description: 'Best practices for defining Payload Globals.'
applyTo: '/src/payload/globals/**/*.ts'
---

# Payload Global Definitions

You are defining Singleton content structures (Globals) for Payload CMS (e.g., Header, Footer, Settings).

## Distinction from Collections
- **Single Document**: Globals have no "list" view. They exist as a single document in the DB.
- **API Access**: Accessed via `/api/globals/{slug}`.
- **Usage**: Ideally used for site-wide configuration, navigation menus, and SEO defaults.

## Best Practices

### Naming
- Slugs should be specific: `mainMenu`, `siteSettings`, `footer`.

### Access Control
- Globals often need `read: () => true` so the public API can fetch the header/footer.
- Update access should be restricted to Admins.

### Localization
- Globals support field-level localization just like Collections.

## Example Global

```typescript
import { GlobalConfig } from 'payload'

export const Header: GlobalConfig = {
  slug: 'header',
  access: {
    read: () => true,
  },
  fields: [
    {
      name: 'navItems',
      type: 'array',
      fields: [
        {
          name: 'link',
          type: 'text',
        },
        {
          name: 'url',
          type: 'text',
        },
      ],
    },
  ],
}
```
