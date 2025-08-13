---
title: "VLM GRPO"
description: "Quelques astuces CSS"
publishedAt: 2025-08-13
category: "Web"
draft: false
image: "/images/schools/polimi.png"
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