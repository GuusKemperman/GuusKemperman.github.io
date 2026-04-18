---
layout: project
name: "Northlight Engine"
---

I joined Remedy Entertainment's core engine team in February 2026 as a Junior Engine Programmer. Northlight is Remedy's in-house engine, powering titles like Alan Wake 2 and Control Resonant. My focus is the scripting system: specifically, making Lua loading **fast**, **safe**, and **stable** for every game built on the engine.

<!-- TODO: Confirm with Jussi that this quote is OK to publish before pushing live. If not, drop the attribution block and use an unattributed paraphrase. -->
<!-- <hr>
<div style="text-align: center; font-size: 1.5em; font-style: italic; margin: 20px 0;">
    "Guus has very independently studied our engine using source code and documentation and learned how key parts work, remarkably well and quickly, and used that information to submit bug fixes and significant, measurable performance improvements."
</div>
<div style="text-align: center; font-size: 1em; font-style: normal; margin: 5px 0;">
    Jussi Sammaltupa, Senior Engine Programmer &amp; Core Team Lead @ Remedy Entertainment
</div>
<hr> -->

# My Contributions

- *[Script Entity Serialization](#script-entity-serialization)*: A 20x deserialisation speedup and a 44% reduction in serialised bundle size, including a subtle C++ string-corruption bug fix along the way.
- *[Asynchronous loading](#asynchronous-loading)*: Static analysis over the Lua AST to auto-identify thread-safe functions, enabling asynchronous script loading.
- *[Throttled Loading](#throttled-loading)*: Spreading script loading across frames to smooth out delivery.
- *[Optimized Script Properties](#optimized-script-properties)*: Halving script-initialisation time in shipping builds.
- *[Stability & Quality](#stability--quality)*: Live-edit crash fixes, better diagnostics, and automated test coverage for the trickier analysis code.

# Script Entity Serialization

Script components are often a special case for saving/loading in engines. Unlike C++ components, where all of the data-members are known at compile time AND consistent accross different instances of components, script components differ based on which script is used. 

Northlight's initial implementation for addressing this problem was robust, but loading the script components was not very performant. Each data member required several runtime lookup and checks, and reqeruied multiple heap allocations to get the date into memory. Out of all the components that were deserialised, scripts would take around **50%** of the total time.

I rewrote the serialization to use a **custom binary format**: a **single heap allocation** for loading each script component, with the data packed tightly. The deserilization of scripts achieved a **20x speedup** with a **44% reduction in disk size**, a win that compounds across every game built on Northlight.

The most interesting part of this work wasn't the format itself, but a bug that surfaced only once loading got fast. The new path was corrupting memory in a way that only reproduced under specific conditions. Tracking it down meant reading through our C++ string implementation and eventually finding a case where a **small-buffer optimisation** interacted badly with embedded null bytes in binary payloads. Fixing it required a careful patch to a very widely-used class, which was paired with a large amount of unit tests.

# Asynchronous Loading

Script loading on the main thread competes with rendering and gameplay for the same frame budget, and a 60 FPS target leaves very little room for it. Moving loading **off the main thread** gets it out of the way entirely. The catch is that any Lua binding authored in C++ might touch shared state in a way that would race against the game loop, and a **data race** in a shipping build is exactly the kind of bug that's hard to reproduce and easy to miss.

I added a `@thread-safe` attribute to the C++ binding layer so individual bindings can be marked, and built a static analysis pass over **Lua's Abstract Syntax Tree** that walks every engine-facing callback and checks whether it, or anything it calls transitively, ever touches a binding that isn't thread-safe.

The check runs at **build time**, so a regression that would introduce a data race gets caught before it reaches a reviewer, not after it ships.

# Throttled loading

Large scenes used to produce **multi-frame stutters** when the engine had to load hundreds or even thousands of scripts at once. The **throttling system** I built spreads that work across frames, giving the main thread headroom to keep delivering frames.

Spreading loading across frames is a significant breaking change, though: it delays the 'readiness' of any given script by possibly hundreds of frames. Changing the timing of initialisation, especially in projects already well into development, could lead to a lot of subtle breakage.

My solution was to isolate 'islands': groups of entities whose scripts only reference each other. None of the scripts in an island receive any events until every script in that island has finished loading, so no script ever observes a half-loaded peer. The timing guarantee the rest of the engine depends on stays intact; only the moment at which an island becomes active gets pushed out. 

The stuttering has been **completely eliminated**, and no game has had to change any of its content to benefit.

# Optimized Script Properties

Northlight scripts expose configurable fields to designers: the values that show up in the editor as tweakable parameters on a component or entity. These are declared as *property blocks* inside the Lua source: structured metadata describing each field's type, default, range, and tooltip. Historically there was quite some overhead every time a script entity was loaded, which meant paying a cost on every launch for a piece of information that is not actually consumed when outside of editor context.

I used Lua's Abstract Syntax Tree to identify these propert blocks, and removed them from the scripts at **export time**. This **halved script-initialisation time** in shipping builds, with no visible change to the authoring experience. It's a small architectural shift that turned a recurring per-launch cost into a one-time build cost.

# Stability & Quality

Alongside the performance work I've been closing out a steady stream of smaller issues. Live-editing especially is something that has received a lot of stability improvements. I believe that the trust in a system and code from other collaborators is what makes them truly outstanding.

I've also been building **unit test coverage** around the scripting systems. The engineering habit of writing tests for invariants that are hard to catch by manual play-testing is something I'm actively trying to push further across the engine.