---
layout: post
title: "Engine Architecture"
date: 2024-11-05
tags: Coral
---

I have iterated many times on the engine architecture before reaching a 'stable' point that could be built upon. I have personally designed the majority of Coral Engine's architecture, and would like to share a glimpse of the final product.

## Ownership

![](/img/projects/y2/coral/EngineOwnershipCleanedUp.png)

There is a clear separation of concerns embedded in the architecture of this engine. There are three types of systems; EngineSubsystems, EditorSystems, and regular systems present in the ECS. They own their own resources to fullfill their own responsibilities with minimum communication between them.

Each regular system in the ECS works in isolation from the other ECS systems. I specifically did not expose a function to get a pointer to a system inside of the registry to encourage this separation. 

The same applies to editor systems; A level editor does not know or interact in any way with a prefab editor, or another level editor.

The only exception is EngineSubsystems. These are essentially singletons, that can be accessed from anywhere in the engine, even other subsystems; The editor subsystem may retrieve an asset from the asset manager to open it for edit. They still have a clear separation of concern, with little overlap between them.

## Cross-platform

I was responsible for the cross-platform support, for everything not related to the rendering. 

**C\++20 to C\++17**

Not all platforms supported C\++20, so I backported the engine back to C\++17. C\++ concepts were the hardest feature to replace, I replaced most of it with good ol' SFINAE instead.

**Same declaration, different definition**

Functions would be declared in header file shared amongst multiple platforms. The function definitions would be placed in a platform specific .cpp file. 

**Same include, different declaration**

In some cases, you want different class layouts per platform (e.g., buffers, shaders & pipelines). We used an ```#ifdef``` to redirect the include to the platform-specific one. 

```cpp
// In StaticMesh.h
#ifdef PLATFORM_WINDOWS
#include "StaticMeshPC.h"
#elif PLATFORM_PROSPERO
#include "StaticMeshProspero.h"
#endif
```

In other cases, we achieved a similar effect by having a baseclass serve as a platform agnostic interface, with platform specific derived classes.

## Engine & Game seperation

I was responsible for separating the engine and the game. The game is a separate project from the engine; The engine is compiled into a static library, and the game links against the engine and compiles into an .exe. 

I've learned an important lesson on managing a large quantity of platforms and configurations: I've used property sheets to avoid repetition. While this took a while to set up, it has made the project easier to configure.

| ![](/img/projects/y2/coral/PropertySheets.png) | ![](/img/projects/y2/coral/EngineAndGameAssets.png) |
|---|---|
| Property sheets overriding changes only for their specific platform or configuration. | There are assets that are unique to each game, but also common assets that are shared between games. |

## Engine & Editor seperation

We have multiple configurations to allow the user to choose whether they want optimisations and/or the editor.

- EditorDebug
- Debug
- EditorRelease
- Release

We want to deliver a well-optimised release build to the designers and artists, while new features are tested in the debug configurations. We removed the editor from the Debug and Release configuration by excluding certain files from compilation, and by the occasional ```#ifdef EDITOR```. A simple, but effective approach!

