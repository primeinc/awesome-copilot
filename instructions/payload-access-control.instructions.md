---
description: 'Security and Access Control Logic instructions for Payload CMS.'
applyTo: '/src/payload/access/**/*.ts, **/access.ts'
---

# Payload Access Control Logic

You are writing security logic to determine who can Create, Read, Update, or Delete (CRUD) documents.

## Logic Flow

### Return Values
- `true`: Access granted.
- `false`: Access denied (throws 403).
- `object` (Where Query): Access granted ONLY to documents matching the query.

### Query-Based Access (Row Level Security)
- Preferred for read and update.
- Instead of checking `if (user.id === doc.owner)`, return `{ owner: { equals: user.id } }`.
- This allows the database to filter results efficiently and prevents fetching unauthorized data.

### Field-Level Access
- You can define access control on individual fields.
- Example: A `notes` field on a User collection that only Admins can read, even if the User can read the rest of their profile.

## Common Patterns

### Admins
- `if (user?.roles?.includes('admin')) return true`

### Public
- `return true` (Use carefully, mostly for read).

### Authenticated
- `if (user) return true`

## Example Access Function

```typescript
import { Access } from 'payload'

export const isAdminOrOwner: Access = ({ req: { user } }) => {
  if (!user) return false

  // Allow admins
  if (user.roles?.includes('admin')) return true

  // Allow users to access their own documents
  return {
    owner: {
      equals: user.id,
    },
  }
}
```
