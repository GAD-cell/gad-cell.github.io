---
title: "KADSI"
description: "KADSI: Knowledge and AI for Dermatology and Skin Inclusion. Our solution for the 2025 edition of the D4GEN hackaton. Awarded 3rd place."
publishedAt: 2025-08-12
category: "Web"
draft: false
image: "/images/projects/kadsi.png"
links:
  - type: "github"
    url: "https://github.com/GAD-cell/KADSI"
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