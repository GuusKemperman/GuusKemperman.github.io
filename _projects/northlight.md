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
- *[World State](#world-state)*: Built the runtime side of the engine's new system for keeping the world in sync with the player's progress, including a custom expression language that is twice as fast as Lua.
- *[Stability & Quality](#stability--quality)*: Made live-editing scripts more reliable and added automated tests for the scripting systems.

# Lua Optimisations

Northlight games run thousands of Lua scripts, and every one of them has to be loaded before it can do anything. Most of my optimisation work was driven by Control Resonant, where it helped bring a 240ms freeze when entering a new area down to at most 5ms. Along the way, loading script components from disk also became 20x faster.

The full story is on the [Control Resonant page](/projects/control-resonant#seamless-area-transitions).

# World State

A game world had to match the player's progress: a quest camp should only appear while the quest was active, and a door should stay open once it had been unlocked. Progress was stored as simple yes-or-no values, called facts, such as whether a quest had started.

Until then, the world could only react to facts in a crude way: an object could require a list of facts to all be true, and anything more complex meant writing Lua. World State was rebuilt from the ground up with better workflows in mind. My focus was the runtime side: making sure the world responded efficiently whenever a fact changed, so it always stayed in sync with the player's progress.

**Reacting to facts without code**

I added components that designers could attach to existing objects, so they could make them react to facts without diving into Lua: for example, hiding a camp once its quest was done, or streaming in part of the level once a quest was completed, and much more. All of this also worked in the editor, so artists could preview the world at different points in the story without starting the game.

**Custom expression language**

Each of these components stored a condition that decided when it applied, such as `quest_open and not quest_done`. To run these conditions, I wrote a small compiler and bytecode interpreter. I chose this over existing languages for four reasons:

- **Speed:** the system had to scale to at least 5,000 objects. These conditions loaded faster than the previous rule-based system, ran about 2x faster than the same conditions in Lua, and about 30% faster than an existing in-house expression language.
- **Simplicity:** conditions should be readable at a glance. Complex logic stayed in Lua; conditions only combined a few facts.
- **Keeping options open:** a simple language could later be moved onto a more powerful one. Going the other way would have meant supporting every feature that existing conditions already relied on.
- **Clear errors:** mistakes were reported in plain language, so designers could fix their own conditions.

# Stability & Quality

Alongside the performance work I've been closing out a steady stream of smaller issues. Live-editing especially is something that has received a lot of stability improvements. I believe that the trust in a system and code from other collaborators is what makes them truly outstanding.

I've also been building **unit test coverage** around the existing scripting systems and the newly added World State systems, and I'm actively trying to push testing further across the engine.
