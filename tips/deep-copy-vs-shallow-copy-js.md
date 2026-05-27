---
title: Deep Copy vs Shallow Copy in JavaScript
slug: deep-copy-vs-shallow-copy-js
author_username: pemba17
date: 2026-05-10
tags: ["javascript"]
---

# Shallow Copy vs Deep Copy in JavaScript — a quick lesson that can save you bugs

A lot of developers use the spread operator to clone objects, but many don’t realize how it behaves with nested objects. 

### Shallow Copy Example

```js
const student = { name: 'pemba', age: 27, contact: { home: '014811677', mobile: '9813637899' } }

const copiedStudent = {...student}

copiedStudent.name = 'PEMBA' // doesn't change the original student.name
copiedStudent.contact.home = '014811678' // changes both copied and original student.contact.home

```

At first glance, everything looks fine. But here's the catch:
- The spread operator (...) only creates a shallow copy
- In Shallow copy nested objects are still referenced, not duplicated

### Solution: Use Deep Copy For Nested Objects

```js

const newCopiedStudent = structuredClone(student)

newCopiedStudent.contact.home = '014811977' // does not change the original student.contact.home

```

### Key Takeaways
- Use shallow copy (...) for flat objects
- Use deep copy when working with nested data
- structuredClone() is a simple way to create a true deep copy in modern JavaScript
