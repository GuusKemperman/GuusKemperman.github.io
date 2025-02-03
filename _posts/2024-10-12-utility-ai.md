---
layout: post
title: "Utility AI"
date: 2024-11-05
tags: Coral
---

> *Utility AI: a decision-making system where agents dynamically evaluate and prioritize actions by calculating context-sensitive utility scores. It is similar to a finite state machine, except the transitions are handled by which state has the highest fitness for a given situation.*

I created the utility AI system for Coral Engine. The system was used to create [the fourteen unique behaviours](https://github.com/BredaUniversityGames/2324-Y2C-PR-Top-Down/tree/dev/Games/ExampleGame/Include/Components/UtililtyAi/States) present in our game [Lichgate](/projects/lichgate).

## Implementation

I wanted to make it easy for programmers and designers to develop AI states. I needed an interface for calling the appropriate functions, for which I used the event system I created earlier. Each state is a different component that can be added to an entity.

[//]: # (TODO: Link to event system)


```cpp
class ChargeUpDashState
{
public:
    // Called at a regular interval. Returning a high score increases the likelihood this state is chosen.
    float OnAIEvaluate(const CE::World& world, entt::entity owner) const;
    
    // Called when this state is entered
    void OnAIStateEnterEvent(CE::World& world, entt::entity owner);
    
    // Called every frame while this state is active
    void OnAITick(CE::World& world, entt::entity owner, float dt);
    
    // Called when this state is exited
    void OnAIStateExitEvent(CE::World& world, entt::entity owner);
};
```

The event system supports C++ and [Visual Scripting](/blog/visual-scripting/), which means the AI behaviour *can* be implemented through a combination of both.

Since the states are implemented as components, our level editor can be used to specify which states the AI can ever be in, to modify the default values of a state, and to inspect how the values change at runtime. 

[//]: # (TODO: Link to level editor)

I focused on **reusing existing systems** to implement the utility AI system; the event system, visual scripting and the level editor. The utility AI system took minimal resources to implement.

## Optimising Evaluation

The intuitive approach for choosing the next state is by going over each entity and evaluating each of it's components.  

![](/img/projects/y2/coral/UtilityAISlow.png)

The problem is that you are wasting performance. [EnTT](https://github.com/skypjack/entt), the **Entity Component System** that we used in Coral Engine, was designed for quickly iterating over components of a given type. Retrieving all the components of a given entity was *very* slow.

This was part of the reason why I chose utility AI; the evaluating process is highly **parallelizable**, and works well with our **Data Oriented Design**. Instead of looping over the entities, we loop over the component types.

![](/img/projects/y2/coral/UtilityAIFast.png)

By sequentially accessing components of the same type, the iteration was sped up drastically. Since the same function is called over and over again, the cache contains more relevant information. This could be further parallelised by using multiple threads, but profiling showed this made little difference for our usecase.

## Conclusion

In the end, I was able to create an easy to use system that was cheap to implement. It was performant enough to support thousands of enemies at a stable FPS in Lichgate with the utility AI system being barely a blip on the profiler.