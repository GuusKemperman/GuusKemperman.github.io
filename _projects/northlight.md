---
layout: project
name: "Northlight Engine"
---

I joined Remedy Entertainment's core engine team in February 2026 as a Junior Engine Programmer. Northlight is Remedy's in-house engine, powering titles like Alan Wake 2 and Control Resonant. My focus is making Lua **fast** and **stable** for every game built on the engine.

<hr>
<div style="text-align: center; font-size: 1.5em; font-style: italic; margin: 20px 0;">
    "Guus learned how key parts work remarkably well and quickly, and used that information to submit significant, measurable performance improvements."
</div>
<div style="text-align: center; font-size: 1em; font-style: normal; margin: 5px 0;">
    Jussi Sammaltupa, Senior Engine Programmer &amp; Core Team Lead @ Remedy Entertainment
</div>
<hr>

# My Contributions

- *[Lua Optimisations](#lua-optimisations)*: Stopped script loading from freezing the game by spreading it across frames, and made loading script components from disk 20x faster.
- *[Stability & Quality](#stability--quality)*: Made live-editing scripts more reliable and added automated tests for the scripting systems.

# Lua Optimisations

Northlight games run thousands of Lua scripts, and every one of them has to be loaded before it can do anything. Most of my optimisation work was driven by Control Resonant, where it helped bring a 240ms freeze when entering a new area down to at most 5ms. Along the way, loading script components from disk also became 20x faster.

The full story is on the [Control Resonant page](/projects/control-resonant#seamless-area-transitions).

# Stability & Quality

Alongside the performance work I've been closing out a steady stream of smaller issues. Live-editing especially is something that has received a lot of stability improvements. I believe that the trust in a system and code from other collaborators is what makes them truly outstanding.

I've also been building **unit test coverage** around the existing scripting systems, and I'm actively trying to push testing further across the engine.
