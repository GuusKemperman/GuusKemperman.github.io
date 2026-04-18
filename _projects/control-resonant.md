---
layout: project
name: "Control Resonant"
---

Control Resonant is Remedy Entertainment's upcoming sequel to *Control*, built on the Northlight engine. My day-to-day is engine work, but the motivating target for a lot of that work is the frame-rate budget on Control Resonant specifically. This page covers the game-facing outcomes of my engine work, along with the custom profiling tool and script-level optimisations I built to drive them.

# My Contributions

- *[Eliminating Multi-Frame Stalls](#eliminating-multi-frame-stalls)*: Collapsing loading-stage script work from multi-frame stalls into something that no longer registers on a frame-time graph.
- *[A Sampling-Based Lua Profiler](#a-sampling-based-Lua-profiler)*: Built a custom profiler to identify Lua bottlenecks in second-to-second gameplay.
- *[Script-Level Performance Wins](#script-level-performance-wins)*: A 10% improvement in second-to-second Lua script performance, driven by data from the profiler above.
- *[Cross-Project Coordination](#cross-project-coordination)*: Rolling engine-wide changes into Control Resonant alongside Remedy's other projects.

# Eliminating Multi-Frame Stalls

One of the most visible wins to the game's frame budget came out of the scripting system. Large batches of scripts loaded at the same time used to stall the main thread for several frames, the kind of hitch that's immediately noticeable. Combining the deserialisation speedup, the throttling system, and the property-block optimisations from my engine work ([see the Northlight page](/projects/northlight) for the details) brought that loading stage down to the point where it no longer registers on a frame-time graph.

# A Sampling-Based Lua Profiler

Existing profiling tools are great at telling you that the engine is spending time in Lua. What they can't do is tell you *which script or which function inside the VM* is responsible. Northlight has an existing Lua profiler, but it was missing some functionality: aggregating over a time range, identifying expensive C++ bindings, and performing line-by-line analysis.

I built a sampling profiler for Lua to close that gap. It periodically snapshots the Lua call stack, aggregates samples per function, and surfaces the hotspots in a format I can hand directly to a gameplay programmer.

# Script-Level Performance Wins

With the profiler in hand, I went looking for opportunities inside the gameplay scripts themselves, not the engine. This was a deliberate crossing of the usual engine/game line: I'm a core-engine programmer by title, but some of the biggest wins available weren't in the runtime, they were in how individual scripts were using it.

The changes were case-by-case, but added up to a **10% improvement in second-to-second Lua script performance**. Beyond the number, this work made the gameplay programmers better equipped to do the same analysis themselves, which is the kind of lasting impact I aim for.

# Cross-Project Coordination

Control Resonant shares the engine with Remedy's other projects, so every engine change I roll in here has to land cleanly on those projects too. In practice this means landing changes behind a switch, enabling them for one project at a time, and working directly with each team to verify before extending. It's slower than a single all-hands rollout, but it's how I've learned to ship scripting-system changes without creating friction for any of the game teams, and it's produced faster real-world iteration than the alternative.
