---
layout: post
title: "Engine Architecture"
date: 2024-11-05
tags: Coral
---

I have iterated many times on the engine architecture before reaching a 'stable' point that could be built upon.

## Ownership

![](/img/projects/y2/coral/EngineOwnershipCleanedUp.png)

There is a clear separation of concerns embedded in the architecture of this engine. There are three types of systems; EngineSubsystems, EditorSystems, and regular systems present in the ECS. They own their own resources to fullfill their own responsibilities with minimum communication between them.

Each regular system in the ECS works in isolation from the other ECS systems. I specifically did not expose a function to get a pointer to a system inside of the registry to encourage this separation. 

The same applies to editor systems; A level editor does not know or interact in any way with a prefab editor, or another level editor.

The only exception is EngineSubsystems. These are essentially singletons, that can be accessed from anywhere in the engine, even other subsystems; The editor subsystem may retrieve an asset from the asset manager to open it for edit. They still have a clear separation of concern, with little overlap between them.

## Cross-platform

I was responsible for the cross-platform support in the first sprint for everything not related to the rendering. The main challenge with cross-platform support involved backporting the engine back to C\++17, of which C\++ concepts were the hardest feature to replace. I needed to make some changes to serialisation as well, as typeids are not platform agnostic.

We used a few different approaches for cross-platform support. The Input.h header for example is shared across platforms, while the cpp file contains platform-specific definitions. In other cases, we used an ```#ifdef``` to redirect the include to the platform-specific one.

```cpp
// In StaticMesh.h

#ifdef PLATFORM_WINDOWS
#include "Platform/PC/Rendering/StaticMeshPC.h"
#elif PLATFORM_PROSPERO
#include "Platform/Prospero/Rendering/StaticMeshProspero.h"
#endif
```

There are buffers, shaders and pipelines that are platform-specific, so the idea was that each platform would have separate members and functions. It would make sense for each platform to have a separate class.

### Did we make the right decision?

The cross-platform rendering architecture should have been implemented differently. This is something we learned from as a team, as our current approach led to an unusually large amount of repeated code. It makes it difficult to implement new features, as you have to write the entire implementation for both platforms. The asynchronous loading of textures on PC only has a few lines that are related to DX12, but it is difficult to combine the common functionality if there is no common class for both platforms. We should have created the platform-specific abstractions on a lower level; buffers, shaders and pipelines could have a platform-specific implementation, to be used by a platform-agnostic StaticMesh. This would have reduced code repetition and created a more easily maintainable codebase.

The project compiles and runs on all platforms. The warning level has been set at the highest level on both platforms, with warnings treated as errors.

## Engine & Game seperation

We discussed with the team what approach to take. 

![](/img/projects/y2/coral/EngineGameDiscussion.png)

The image above shows the pros and cons of each approach. In the end, we decided to compile the Engine into a static library.

I was responsible for separating the engine and the game. The game is a separate project from the engine. I've learned an important lesson on managing a large quantity of platforms and configurations; I've learned how to use property sheets to avoid repetition. While this took a while to set up, it has made the project easier to configure.

| ![](/img/projects/y2/coral/PropertySheets.png) | ![](/img/projects/y2/coral/EngineAndGameAssets.png) |
|---|---|
| Property sheets overriding changes only for their specific platform or configuration. | There are assets that are unique to each game, but also common assets that are shared between games. |

### Did we make the right decision?

In the end, we spent around half a week separating the engine and game into separate C++ projects. But in our final demo, there was almost no game-specific-C++ used, everything was powered through visual scripts instead. The separation of engine and game assets has been immensely useful. We should however have more seriously considered the option of having our game consist of assets alone, with each game having its own separate assets folder. 

While the separation of engine and game on the C++ side has not paid off during the current block, we expect that it will become more useful during the last block. Our game will grow more complex over time, and it is nice to have the option to move complex systems to C++.

## Engine & Editor seperation

We have multiple configurations to allow the user to choose whether they want optimisations and/or the editor.

- EditorDebug
- Debug
- EditorRelease
- Release

We want to deliver a well-optimised release build to the designers and artists, while new features are tested in the debug configurations.

We removed the editor from the Debug and Release configuration by excluding certain files from compilation, and by the occasional ```#ifdef EDITOR```.

### Did we make the right decision?

The separate configurations for the editor are confusing to someone new to the engine, especially as Visual Studio picks the Debug configuration by default the first time you start the project. The tradeoff is that we have a much faster editor and that we are able to test release builds with all the functionality the editor provides.

In hindsight, the separation of the engine and the editor could have been made a lot cleaner. Every time you add an editor-only file, you have to *remember* to exclude it for all platforms. We could have had a separate project for the editor, which would have helped with more strictly enforcing the separation.

## Rendering refactor

The rendering architecture of the engine had been mostly neglected during the initial months. There was a lot of technical debt from having survived an OpenGL implementation, an XSR rewrite, before continuing on to a cross-platform DX12 and Prospero implementation. There were functions being called that were just empty, there were classes that had grown redundant, or whose names no longer matched their functionality, and there was no shared data between worlds at all; which sounds good in practice, but was taken to such an extent that shaders were compiled separately for each world.

Since we expected this project to last at least four more weeks, possibly twelf, it was justified to allocate resources to refactor and improve the rendering backend. Marcin, one of our graphics programmers, proposed a new architecture and discussed it with me, as I was very involved with the initial implementation and overall engine architecture.

![](/img/projects/y2/coral/RenderRefactorFinal.png)

His implementation was well thought out but required some adjustments. He got rid of all the buffers owned by the world and made them all shared instead. I pointed out that there is definitely still data that needs to be unique per world; their framebuffers, the size and position of the viewport and the debug lines buffer. We discussed some alternative solutions and ended up deciding on the GPUWorld itself being responsible for the rendering, and accessing the shared resources in Engine::Renderer as needed.


## Conclusion

I have personally contributed to the majority of this Engine's architecture. By keeping each system separate from each other and preventing communication between them, we ensure that each system acts only on the data that it should be concerned about. I have struggled in the past with separating concerns when the systems were closely tied together, but I have learned how to do so during this project.

I was responsible for getting the engine to compile for PS5 while our graphics programmers worked on implementing cross-platform rendering. We have created a product that compiles and runs without errors or warnings on all target platforms. We learned lessons along the way, and in hindsight, we regret the way we have implemented cross-platform rendering.

In order to create a clear distinction between engine and game, by creating separate projects and asset folders. I learned about property sheets, and how they can be used to better maintain the different configurations and platforms we support while having little repetition in our engine and game .vcxproj files. We used a much more primitive approach to separate the editor from the engine, but this primitive approach still remained effective and simple to use. I have learned more about separation, and in future codebases, I would instead choose to make the editor a separate project.

We very organically came to a point where we sat down to discuss important design decisions. We used visual explanations where needed, went back and forth to discuss the pros and cons of each approach, and were always able to settle on a design direction. Documented examples included the implementation of the utility AI system and the rendering refactor, but many more discussions were held throughout the sprints.
