---
description: 'Guidelines for building public-facing APIs and Server Actions in Payload.'
applyTo: '/src/app/api/**/*, /src/app/(app)/**/*, /src/actions/**/*'
---

# Payload Public API & Server Actions

You are writing server-side code that interacts with Payload CMS data to serve public users.

## Data Fetching Strategy

### Local API (Preferred)
- Always use the Local API (`payload.find`, `payload.create`) instead of REST/GraphQL when running on the same server (Next.js).
- **Performance**: Local API bypasses the HTTP layer, reducing latency and serialization overhead.

### Request Context
- In Next.js App Router, initialize Payload with the config promise.
  ```typescript
  import { getPayload } from 'payload'
  import configPromise from '@payload-config'

  const payload = await getPayload({ config: configPromise })
  ```

### Depth Control
- Be extremely careful with the `depth` parameter.
- Default depth is often 2. Set `depth: 0` if you only need IDs for performance.
- Large depths can cause exponential database queries.

## Security & Validation

### Validation
- Never trust client input. Use Zod or Payload's validation within the Local API.
- `payload.create` runs field-level validation automatically.

### Access Control
- Local API runs with `overrideAccess: true` by default (Admin privileges).
- **Crucial**: If handling user requests, you **MUST** manually check permissions OR pass `req.user` and set `overrideAccess: false` if you want to rely on Collection Access Control.

## Example: Server Action

```typescript
'use server'
import { getPayload } from 'payload'
import configPromise from '@payload-config'

export async function submitContactForm(formData: FormData) {
  const payload = await getPayload({ config: configPromise })

  try {
    await payload.create({
      collection: 'contact-submissions',
      data: {
        name: formData.get('name'),
        email: formData.get('email'),
      },
      // overrideAccess: false // Uncomment to enforce collection permissions
    })
    return { success: true }
  } catch (error) {
    console.error(error)
    return { success: false }
  }
}
```
