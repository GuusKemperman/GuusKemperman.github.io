---
layout: post
title: "How exceptions make your game faster"
date: 2024-07-15
tags: Coral
---



There are a number of ways to determine *when* to load an asset, and Coral Engine has seen all of them. I want to share some of the approaches we have taken, how effective they were or how they fell short.


## Load on request

The user calls GetAsset("Hello"), you load asset "Hello".

## Load on dereference

More spread out, no loading assets that are never referenced.

## Load asynchronously

Trickier to implement. Textures benefited the most from this approach, as you can provide a dummy texture while the real texture is still loading.

## Load in bulk

Simpler, but requires a dependency graph.

