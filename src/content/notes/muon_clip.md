---
title: "Muon clip"
description: "Quelques astuces CSS"
publishedAt: 2025-08-13
category: "Web"
draft: false
image: "/images/schools/polimi.png"
links:
  - type: "github"
    url: "https://github.com/GAD-cell/muon-clip"

---

Here are some TypeScript patterns I've found particularly useful:

## Type Predicates

Type predicates are functions that narrow down types:

```typescript
function isError(value: unknown): value is Error {
  return value instanceof Error;
}
```

## Discriminated Unions

Perfect for handling different states:

```typescript
type State = 
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success', data: string }
  | { status: 'error', error: Error }
``` 