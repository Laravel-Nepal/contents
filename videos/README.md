# Videos

Community videos, tutorials, or recorded meetups.

## File format
- One file per video → `slug.md`
- Use Frontmatter for metadata.

## Author guideline
- Profile of the video author(s) should be in the `authors/` folder.
- Use the `author_username` field to reference the author's `username` from the [`authors`](../authors) folder.

## Example
```markdown
---
title: Intro to Laravel Livewire
author_username: achyut
youtube: xxxxxxxx // YouTube video ID
date: 2025-08-30
tags: ["laravel", "livewire"]
---

A beginner-friendly introduction to Laravel Livewire.
```