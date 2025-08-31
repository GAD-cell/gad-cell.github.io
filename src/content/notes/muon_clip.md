---
title: "Muon clip"
description: "Implemented the Muon optimizer in PyTorch, incorporating the latest research improvements (QK-clipping from Kimi K2, improved Newton–Schulz orthogonalization with gradient matrix eigenvalue estimation). Supports distributed training with PyTorch and DeepSpeed, optimized for scalability."
publishedAt: 2025-08-14
category: "Web"
draft: false
image: "/images/projects/muon_clip.png"
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