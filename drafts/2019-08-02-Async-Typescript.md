---
layout: post
title: Bare-bones Node with async Typescript.
---

Sometimes you just want something simple. A simple little script that will make your life easier. Maybe you just want to read a file, or hit an api, without dragging every man and his dog into it. But, you want to use the technologies you are use to, but without having to master a new library or framework.

In this blog I will build a bare-bones Node script that uses modern async Typescript.

### Versions

With the node and typescript ecosystems in constant flux, you have to check that you are playing with the correct versions.
Typescript: Async / Await is supported has been supported by TypeScript since version 1.7. With TypeScript 2.1 it was no longer required to target ES6.
In the sample package.json the Typescript dependance is `json "typescript": "^3.3.3333"`
The compilation target in the tsconfig.json is set to ES6, so we should be good with that.
