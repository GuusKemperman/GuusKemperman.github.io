---
layout: project
name: "RTS 2D"
---

The very first project I made while studying at Breda University of Applied Sciences.
It is incredibly satisfying to watch thousands of AI fight against eachother.

# Features

- *[AI](#ai)*: Thousands of AI autonomously battling eachother.
- *[Renderer](#renderer)*: A software renderer written entirely on the CPU.
- *[UI](#ui)*: A lightweight UI system made specifically for this game.
- *[Physics](#physics)*: A custom physics implementation capable of supporting thousands of units.
- *[Editor](#editor)*: Integration with Tiled for faster level development.

# AI

The behaviour behind each individual unit is relatively simple. The underlying system uses a finite state machine, brought to life using [steering behaviours](https://www.red3d.com/cwr/steer/).

![First real battle](/img/projects/y1/blocka/Week 5 - First real battle_Segment_0_gif.gif)

I support several unit types, with different ground troops and flying vehicles. Each unit type can be defined in a .config file, making the system incredibly easy to extend with new unit types.

![](/img/projects/y1/blocka/aiconfig.png)

Besides individual behaviour, AI can also work together and form groups. I am incredibly happy with the movement system, a system that took several iterations to get right. I implemented autonomous steering behavior, which ended up looking really natural. Their movement inside a formation also works really well, even with different types of units.

![Previews and rotation](/img/projects/y1/blocka/Week 5 - Previews and rotations_Segment_1_gif.gif)

The slots are assigned in such a way to minimise the overall travel time of the entire group, taking into account travel time, distance, and if any units would be blocking eachother. This was a suprisingly difficult heuristic to get calculate quickly and accurately, but the final result became fun to play around with.

![Improved group movement](/img/projects/y1/blocka/Week 4 - Improved group movement 4_Segment_0_gif.gif)

Their collaboration goes further than just movement; each unit will alert nearby friendlies if they detect enemy activity, and start an investigation.

![Investigating](/img/projects/y1/blocka/Week 5 - Investigating_Segment_0_gif.gif)

# Rendering

Similarly to my [Snow Man's Land project](/projects/snow-mans-land.html), this game was rendered entirely by the CPU, which means that every frame I was responsible for letting my program plot each individual pixel. 

A CPU is not fantastic at rendering (that's why they invented the GPU!), and a lot of optimisations were required to get to a point where we can render thousands of units at a stable FPS. There was even some room for fancy visual effects:

![Smoke](/img/projects/y1/blocka/Week 4 - Smoke_Segment_0_gif.gif)

# UI

The in-game UI featured simple sprite-rendering, buttons, a dynamic cursor, and an interactable mini-map

![Dynamic cursor](/img/projects/y1/blocka/Week 7 - Dynamic cursor_Segment_0_gif.gif)

The layout of a canvas was not hardcoded, but could instead be loaded from a configuration file, making it easily adjustable.

![](/img/projects/y1/blocka/uiconfig.png)

# Physics

This was the first time I was faced with the sheer overhead of handling thousands of colliders. A brute-force approach scales horrible, and I directed my focus towards implementing my first acceleration structure. I researched several possibilities, and ended up implementing a quad-tree.

![Quad trees](/img/projects/y1/blocka/Week 3 - Quad trees_Segment_0_gif.gif)

It sped up the collisions tremendously! In hindsight, I should have stuck with a simple 2D grid; I had a fixed world-size and all my units were the same size. The memory usage would have been higher, but it would have been much faster.

A benefit from having a software renderer is that you work closely with the actual pixels. In [Snow Man's Land](/projects/snow-mans-land.html) I used this to build snowmen pixel-by-pixel, and I found another great use-case for this game; pixel perfect collision!

![Pixel Perfect Collision](/img/projects/y1/blocka/Week 4 - Pixel collision_gif.gif)

I essentially draw both sprites to a local buffer. If one sprite is drawing to a pixel that has already been drawn before, you have found an overlap. To make these collisions more efficient, I first use a quadtree and simple box collisions to see if pixel-perfect is even needed.

The game is still able to run at more than one hundred fps with a thousand units on screen. While I know there are still a lot of optimizations I could make for the next block, the performance between my intake assignment and this project has improved so much that I would consider the performance an achievement already.

# Editor

I noticed I was spending a *lot* of time manually typing in the tile-types for each map. I investigated possible tools I could use to speed up development, and ended up using Tiled as a level-editor.

![Using Tiled](/img/projects/y1/blocka/Week 4 - Using Tiled_Segment_0_gif.gif)

Adding support for importing Tiled exports, made creating levels a thousand times easier.


