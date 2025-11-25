---
description: 'Guidelines for developing custom components within the Payload CMS Admin Panel.'
applyTo: '/src/payload/admin/**/*, /src/app/(payload)/admin/**/*, **/*.client.tsx'
---

# Payload Admin UI Development

You are working within the Payload CMS Admin Panel context. This environment is a React Single Page Application (SPA) embedded within Next.js (App Router).

## Core Constraints

### Client Components
- Most custom components (Fields, Views, Cells) **MUST** be Client Components.
- Always start files with `'use client'`.
- You cannot import server-only modules (like `fs` or direct DB adapters) here.

### Component Library
- Use `@payloadcms/ui` for UI primitives to match the native design system.
- Common imports: `Button`, `Gutter`, `RenderCustomComponent`, `useModal`.

### Hooks & Data
- **Forms**: Use `useForm` from `@payloadcms/ui` to access the entire form state.
- **Fields**: Use `useField({ path })` to get/set values for specific fields.
- **Config**: Use `useConfig()` to access the sanitized Payload config on the client.
- **Auth**: Use `useAuth()` to get the current logged-in user.

## Styling

### SCSS / Modules
- Payload uses SCSS modules internally.

### Tailwind
- If configured, you can use Tailwind classes, but ensure they don't conflict with Payload's base styles.
- Prefixing is recommended if not using a scoped build.

## Example: Custom Text Field

```typescript
'use client'
import React from 'react'
import { useField, TextInput } from '@payloadcms/ui'

type Props = { path: string; label: string }

export const CustomTextField: React.FC<Props> = ({ path, label }) => {
  const { value, setValue } = useField<string>({ path })

  return (
    <div className="custom-field">
      <label>{label}</label>
      <TextInput
        path={path}
        value={value || ''}
        onChange={(e) => setValue(e.target.value)}
      />
    </div>
  )
}
```
