---
title: "VLM GRPO"
description: "An implementation of GRPO for VLMs training, compatible with unsloth."
publishedAt: 2025-08-12
category: "Web"
draft: false
image: "/images/projects/vlm_grpo.png"
links:
  - type: "github"
    url: "https://github.com/GAD-cell/vlm-grpo"

---


Some modern CSS tricks I use frequently:

## Container Queries

```css
.card-container {
  container-type: inline-size;
}

@container (min-width: 400px) {
  .card {
    display: grid;
    grid-template-columns: 2fr 1fr;
  }
}
```

## Custom Properties with Fallbacks

```css
.element {
  background: var(--custom-bg, #ffffff);
  color: var(--custom-color, var(--fallback-color, #000000));
}
``` 