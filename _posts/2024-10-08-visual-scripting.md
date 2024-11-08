---
layout: post
title: "Visual Scripting"
date: 2024-11-05
tags: Coral
---

I spent around eight weeks on implementing visual scripting. The backend was made entirely from scratch, while the frontend used existing ImGui plugins.

## What can this tool do?

**Demo**

The scripting tool is designed to be general purpose. There is a wide variety of games that can be implemented using this tool. I have created three very simple demos of various complexities to show what is possible through scripts.

![](/img/projects/y2/coral/W4_MovingAndSpawningPrefabsDemo.gif)

*The player movement implemented through scripts. Pressing spacebar spawns prefabs around the player, showcasing that scripts can reference and use assets*

![](/img/projects/y2/coral/W7_StressTestEnvironment.gif)

*A very simple demo. Enemies move around from point to point, and select a new random point once their target has been reached. The user can shoot and kill enemies, who are then respawned. Everything was implemented through scripts, including the enemy behaviour, the player movement, the collision and the respawning of the enemies.*

![](/img/projects/y2/coral/W8_Timelapse.gif)

*A timelapse showing the creation of the demo intended for the showcase day. The demo took 1:40 hours to make.*

![](/img/projects/y2/coral/W8_Demo2.gif)

*The result of the above timelapse. A simple top-down game. The user can walk around, collect coins, and purchase weapons. Crates can be destroyed to acquire more coins. Once again, **everything** was implemented through scripts.*

**Scripts can be interacted with from C++**

There is no distinction between a reflected C++ class and a compiled script once they are inside the runtime reflection system, which means you do not need to write more code to account for the script components; a function that serializes components by iterating over their members does not need to know or care whether the underlying component is a script or a C++ class, it just serializes it. I wrote my own runtime reflection system to achieve this.

Script functions can be easily called. Error checking has been left out for brevity's sake.

```cpp
const MetaType& const type = *MetaManager::Get().TryGetType(scriptName);

for (int32 i = 0; i < 10; i++)
{
	FuncResult constructResult = type->Construct();
	MetaAny& instance = constructResult.GetReturnValue();

	const MetaFunc& const func = *type->TryGetFunc(funcName);
	FuncResult result = func(instance, i);

	if (*result.GetReturnValue().As<int32>() != i)
	{
		return Result::FAILURE;
	}
}

return Result::SUCCESS;
```

**Scripts can interact with other scripts**

![](/img/projects/y2/coral/W3_IsOdd.png)

*Script functions can be called from other script functions. Here IsEven is a function created using scripts and is called from an IsOdd function.*

**Mix and match**

You can write a game entirely using C++, entirely using scripts or a mix of both. You can call and access C++ functions and fields from scripts, and call and access script functions from C++. This allows programmers to develop complicated or computationally expensive gameplay elements, while designers can prototype and rapidly iterate using the scripting tool.

**Searching**

![](/img/projects/y2/coral/W5_Searching.gif)

*A search bar has been implemented to speed up development*

**Direct integration with entt**

The components live inside of an ```entt::storage``` specialized for script components.  There is no indirection, the memory layout is the exact same as if the component were a C++ class. Possibly even better, because unlike in C++, the order of the fields are shuffled to minimise padding.

![](/img/projects/y2/coral/W4_ScriptAddedAsComponent.png)

*Scripts can have hold data. They can be added as a component to entt::registry*

You do not have to take into account the edge case of script components anywhere in your code. ```entt::registry``` offers the ```storage()``` function to iterate over all the storages. This is useful when you want to inspect or serialize a registry. Since our scripts are stored inside one of these, they can be accessed through the call to storage.

```cpp
void Engine::Archiver::Serialize(const Registry& registry, BinaryGSONObject& gsonObject)
{
	for (auto&& typeStoragePair : registry.Storage())
	{	
		// We can use the same function to serialize both C++ classes and 
		// script classes.
		SerializeStorage(registry, gsonObject, typeStoragePair);
	}
}
```

**Undo, redo, copy, cut, paste and duplicate**

 A selection of nodes and links can be copied and pasted, allowing for rapid refactoring and expansion of your scripts. If you ever make a mistake, each action can be undone/redone using CTRL+Z/CTRL+Y. The do-undo has been implemented using the memento pattern.

![](/img/projects/y2/coral/W6_CopyCutPasteDuplicateUndo.gif)

**Errors and checks**

I have written and mantained around a dozen unit tests using the visual scripting tool. They allow me to catch mistakes and bugs early on.

The return value of functions can be checked and validated using the IsNull node.

![](/img/projects/y2/coral/W4_GettingTransformAndIsNullCheck.png)

*If the user does not have a transform, IsNull would return true in the script above.*

But no need to remember to check for null constantly; if you ever forget, a helpful message will be printed to the console. Some errors can even be caught at compile time, making your scripts less error-prone.

![](/img/projects/y2/coral/W5_LogMessageTakesYouToError.gif)

*Pressing it navigates the focus directly to the source of the error.*

**Reroutes**

![](/img/projects/y2/coral/W6_After.png)

*Reroute nodes can be used to clean up your scripts*

## How does it work?

The Visual Scripting uses a node based interpreter. I follow the execution links while recursively walking up the tree where needed to obtain the outputs from any pure nodes. 

I initially wanted to go for a bytecode based approach; it's a subject I'd been interested in for a while and one I wanted to learn more about. There is an opportunity for an optimization pass to shift more of the workload to compile time. 

In the end I decided against it, it's an additional layer of complexity that makes the system more difficult to debug and extend. The idea was that if the performance was lacking, there might be more benefit to transpiling to C++, or to use LLVM directly. 

The node based interpreter still benefits from plenty of optimisations, for example by caching outputs, allocating on a stack and using RVO. I have additional optimizations for trivial types, by for example avoiding the indirection of invoking the copy constructor when a memcpy will suffice. More on this in the next blog, visual scripting optimisations!