# Posts

Blog articles, tutorials, and long-form content.

## File format
- One file per post → `slug.md`
- Use Frontmatter for metadata.

## Author guideline
- Profile of the package author(s) should be in the `authors/` folder.
- Use the `author_username` field to reference the author's `username` from the `authors/

## Images guideline

- Recommended size: 1200 x 630px (for social sharing)
- Place cover image in the `/images/posts` folder. The file name must be same as the `slug` field.
- Place other images required for the post in the `/images/posts/{slug}/` folder where `{slug}` is the post's slug.

## Example
```markdown
---
title: Getting Started with Inertia.js in Laravel
author_username: achyut
date: 2025-09-01
tags: ["laravel", "inertia", "frontend"]
---

Inertia.js allows you to build modern single-page applications using classic Laravel routing...
```