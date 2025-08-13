---
title: "Luth"
description: "Quelques astuces CSS"
publishedAt: 2025-08-13
category: "Web"
draft: false
image: "/images/schools/polimi.png"
links:
  - type: "github"
    url: "https://github.com/kurakurai/Luth"
  - type: "hf"
    url: "https://huggingface.co/kurakurai"
---


Quick reference for common git workflows:

## Feature Branch Workflow

1. Create feature branch:
```bash
git checkout -b feature/new-feature
```

2. Regular commits with conventional commit messages:
```bash
git commit -m "feat: add new feature"
git commit -m "fix: address review feedback"
```

3. Rebase before merging:
```bash
git fetch origin main
git rebase origin/main
``` 