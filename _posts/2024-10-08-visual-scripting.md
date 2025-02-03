---
layout: post
title: "Visual Scripting"
date: 2024-11-05
tags: Coral
---

I spent around ten weeks on implementing visual scripting. I aimed to replicate the functionality and workflow of Blueprints, to make gameplay programming accessible to non-technical designers. 

## What can this tool do?

I ensured that you can write a game entirely using C++, entirely using scripts or a mix of both. This allows programmers to develop complicated or computationally expensive gameplay elements, while designers can prototype and rapidly iterate using the scripting tool. 

The visual scripting enabled designers to implement player movement and the *many* abilities featured in Lichgate, all without needing assistance of programmers. 

In the end, more than ~90% of the gameplay in Lichgate was achieved through visual scripting, powered by 156 different scripts. 

<div class="video-as-gif-container">
  <video autoplay loop muted playsinline>
    <source src="/img/projects/y2/coral/DifferentScripts.mp4" type="video/mp4">
  </video>
</div>

The tool supports for-loops, while-loops, branches (if/else), getting & setting variables, function calls, and events.

**Script Components**

Scripts can be added as components to entities. To achieve this, I [made some modifications](https://github.com/GuusKemperman/CoralEngine/blob/39a86d7ff667518a458fa276a4fa1fa8066d8de5/Source/World/Registry.cpp#L25) to **[EnTT](https://github.com/skypjack/entt)**, the ECS library that we used. Now, script components are stored in the same way as C++ components. You don't need to handle both C++ and script classes for the inspector and serializer, because you access them using the same interface!

**C++ Bindings**

I made the API for exposing C++ to scripting as simple as could be, using the [runtime reflection](/blog/runtime-reflection) system I'd written earlier. And ofcourse, scripts can always interact and communicate with other scripts.

```cpp
type.AddField(&Player::Health, "Health")
  .GetProperties()
    .Add(Props::sIsScriptableTag);

type.AddFunc(&Player::Respawn, "Respawn")
  .GetProperties()
    .Add(Props::sIsScriptableTag)
```

You can also go the other way; call and access script functions and fields from C++. This was especially useful for writing and maintaining the dozens of **unit-tests** that tested the visual script interpreter. 

## Editor

I designed the workflow and the editor to be similar to Unreal Blueprints. I used the [Node Editor in ImGui](https://github.com/thedmd/imgui-node-editor) library as a starting point.

**Context aware search**

I created a responsive and context aware search bar, greatly improving ease of use and development speed. The searching happens fully **asynchronously**. Options that are picked more frequently, or options that make more sense given the existing function, are ranked higher. 

<div class="video-as-gif-container">
  <video autoplay loop muted playsinline>
    <source src="/img/projects/y2/coral/ScriptEditor.mp4" type="video/mp4">
  </video>
</div>

**Shortcuts**

I included useful shortcuts; nodes can be copied and pasted, allowing for rapid refactoring and expansion of your scripts. Every action can be undone/redone. Reroute nodes can be used to clean up your scripts

![](/img/projects/y2/coral/W6_CopyCutPasteDuplicateUndo.gif)

**Errors**

Any runtime or compile time errors in your scripts will be printed to the console. Pressing it navigates the focus directly to the source of the error.

![](/img/projects/y2/coral/W5_LogMessageTakesYouToError.gif)

## Virtual Machine

The Visual Scripting uses a node based interpreter. The virtual machine can be found in it's entirety in [VirtualMachine.cpp](https://github.com/GuusKemperman/CoralEngine/blob/main-lite/Source/Core/VirtualMachine.cpp).

**Type erasure**

Everything in the virtual machine is, from a C++ perspective, completely type-erased. C++ objects are represented using a ```MetaAny```, essentially a ```void*``` with some additional type information. To give the virtual machine the ability to still call C++ functions using these, I used C++ **templates**. For those interested, a significantly simplified standalone implementation can be found below.

<iframe width="100%" height="600px" src="https://godbolt.org/e#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGISQBspK4AMngMmAByPgBGmMQgAKxmpAAOqAqETgwe3r7%2BQemZjgJhEdEscQnJtpj2JQxCBEzEBLk%2BfoG19dlNLQRlUbHxSSkKza3t%2BV3j/YMVVaMAlLaoXsTI7BzmAMypxEzALEwA1AKbJhoAgrvhyN5YJyY7blReDA7ZYs/YlzdmOzuD0wTxehgAnj8/rcPsDQW4AG6YBwkKHXP73JgKBQnK4xcYHBwAMXepnRAHYrOiNABOAiYFipAz055uAjg1KMVgggAqpBOmQAXpgAPoEE4ASTRN1ps0cyBOPJOcgYqSYyAA1kJwsB6BA0AxxgKCOgQCAkSjiKzxqaQBCoWYAicWsAFEtobSTJSPTTLjSDUabWb7Y7nQxwaCACLO4iukyJCxSxKR55UmW%2B2nETAEdYMY22iEi0TjVk8n4QCHunZpv1elPU9P0xnMkGs9mc5hsE4AJWz/PbXLYADoR7jYwp%2BULRQQR0PJQx8JsFNK/XK8Aqg3bw/OEagNZgJc39QJAyazW8Pg1Wb2CBAruPZ1XfqGL58BDzUBKGLv96QfX6A3FTcLSIK0Xk3e0dmfJ0XQnf9ZTPEBwiwVQRSUABHLxGAuF4v0XTAFFnH53WpX1vVIv0sxzYg81fBoPy/H9MAgFU1U1bUjHoVl71dfk8PXAjy1gpZH1Tf9yXrGUrlSLwYlodcQB9JsmSYFkXgHTsQRvfsOUHTBZzHOMoJ9PECXVAgSQ%2BCBNzo7Jr2zO8HxHJ8TlfJYTkUijaXCJjiAgeCM19BNX3jSNj0NIDEJA1FwMQyDoJjV13IAWh%2BfNg3DALa3I9Na09BDbWOfcRWQzBUIwrCPkwa08GFVAqFnRykrSirsOq70JLErzAso7Ncx3PcDyPV9%2BVgydMEwtqqxrfKeokkjcpy7LJJ9CDt1QTkDlAiAlnC09bWisC3DWyFjNDYSTkA8SZsCqj%2Bp8wa/OErrFpW659jwBFVPYVbENsgRrTi8M9sig7kVAwGC0ys6Ahch79ytDqU2raFOpRhtwiAnwIEx51%2BSoWhUFUk4YgWr00xOSmTjumjnSeSwSZeusPSuXHjnCHbUYpqmbNJBoTgHdARRCyl6YCBQfCeNHucp0yCEJCzSWdfF5fM4XSXJgXdKF18peRmX0q3CMswl2hxWeaMmBVhX1aszWTs1rgND1ycgchUXEiHDQqD1utpr%2BKnqb62mTqLLECFZTHyxNrwzf9ilIw4FZaE4RJeD8DgtFIVBODcaxrAFNYNlbAEeFIAhNCTlYNVGIdySdgJAgCHYAi4AAORJEn0ThJHTyvs84XgFBADRy8rlY4FgJAyuRLxQPIShYOUQw6iEBBUAAdwzsu0EZOhVOyZeIloNfN4zrPd9SOgRgRZBUlSEUES4GkRRbcYRVURvSEv6/iEiblB7f1QHvegxAADyc9T5b37jPZAPFh4cF4LApo%2BAM68H4IIEQYh2BSBkIIRQKh1CZx0HoAwRgUD50sPoPA%2BJ4AQE0iARgBACCkCRAkeW7wNRLBWBtBoCCAD0QZTCWGsG/AgvBUBsOIHgLAw9IArGIO8RwbAPyeDkTtVY6xNh6BtOEI%2Bq917QO4LwDeBxUicB4MnVOfdiEDw4NgVQs9QInE/pIE4LAFC3xOE/GkQ4xEnAgHnERVCTi4EICQemOwuBLF4BXYh3DSAIEwEwLACQNE10kOSIcZg240h2IkHYLcdhtykF3FOHBe6kHPhIwBw9R5xK0Ak8pZgbFZxzogse8SVhsMyM4SQQA"></iframe>

The actual implementation of ```MetaFunc``` is more complete, it includes type-safety and various optimisations. I extended C++ [copy-elision](https://en.cppreference.com/w/cpp/language/copy_elision) to construct the return value of nodes directly into the right place in the stack, without having to copy or move the variable at all. 

The full implementation can be found here:
- [MetaFuncFwd.h](https://github.com/GuusKemperman/CoralEngine/blob/main-lite/Include/Meta/Fwd/MetaFuncFwd.h)
- [MetaFuncImpl.h](https://github.com/GuusKemperman/CoralEngine/blob/main-lite/Include/Meta/Impl/MetaFuncImpl.h)
- [MetaFunc.cpp](https://github.com/GuusKemperman/CoralEngine/blob/main-lite/Source/Meta/MetaFunc.cpp)

**Graph traversal**

For traversing the graph, I follow the execution links (the white lines), which indicate the order to execute nodes in. The output of these executed nodes are stored directly on a stack. Variables stored in this stack can be passed on to future nodes that need to be executed. 

Pure nodes, nodes without an execution link, are executed whenever it's output is required for another node. Because pure nodes do not modify the state of the world or engine, their results are cached; no need to call that function again until we run a impure node and the state of the world or engine changes.