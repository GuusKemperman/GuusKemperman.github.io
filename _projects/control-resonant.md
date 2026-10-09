---
layout: project
name: "Control Resonant"
---

Control Resonant is Remedy Entertainment's sequel to *Control*, built on the Northlight engine. From February to September 2026, much of my engine work focused on its frame rate.
# My Contributions

- *[Seamless Area Transitions](#seamless-area-transitions)*: Helped optimise the game's seamless area transitions, cutting a 240ms freeze down to at most 5ms.
- *[Script-Level Performance Wins](#script-level-performance-wins)*: Improved second-to-second Lua performance by 10%.

# Seamless Area Transitions

Whenever the player enters a new area, the game loads thousands of scripts that bring it to life. All of this work used to happen in a single frame, freezing the game for **240ms**, roughly a quarter of a second.

| [![](/img/projects/y4/control-resonant/before.png)](/img/projects/y4/control-resonant/before.png) | [![](/img/projects/y4/control-resonant/after.png)](/img/projects/y4/control-resonant/after.png) |
|---|---|
| Before: a 240ms spike | After: at most 5ms |

*Blue is total frame time, orange is time spent on scripts. Note the different vertical scales.*

**Doing less work: 240ms to 35ms**

I cached results instead of recomputing them, sped up the slowest parts of loading, and moved work to export time, so it is done once when the game is built rather than every time an area loads.

**Spreading the work out: 35ms to at most 5ms**

Rather than loading everything at once, the game now loads a small batch of scripts per frame until the area is ready. The challenge was doing this without changing how the game behaves.

My solution was to use static analysis to find groups of scripts that depend on each other. A group only starts running once every script in it has loaded, so no script ever sees another one half-ready. The game's behaviour stays the same; the group just becomes active a little later.

Static analysis only gets you so far, so I paired it with runtime debugging tools that automatically catch the edge cases it misses.

# Script-Level Performance Wins

Some of the biggest wins were not in the engine itself, but in how gameplay scripts used it. Using a sampling profiler, I identified a few of the worst bottlenecks and optimised them one by one. Together, these changes improved second-to-second Lua performance by **10%**.
