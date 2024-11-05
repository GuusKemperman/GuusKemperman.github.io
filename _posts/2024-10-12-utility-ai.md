---
layout: post
title: "Utility AI"
date: 2024-11-05
tags: Coral
---

# Utility AI

I wanted to describe our AI design discussion, as they neatly followed the usual steps taken when developing a new feature.

## Requirements

We wanted a simple way to create complex AI behaviour in our engine. I had a few discussions with Alfonso and Alexandru on what such a system requires.

1. It should be easy to inspect states, to see their values and modify them through the editor
2. Designers should be able to create their own states and behaviours.
3. Designers should be able to select which states a particular type of AI could ever be in.
4. You should be able to write states both in the engine project and in the game project.

## User workflow

A common approach to AI is to use finite-state machines. These can quickly grow quite large and difficult to manage without a proper FSM editor, which is out of the scope of our current block. The transitions need to handle various edge cases, and the list of conditions specifying when to transition can grow quite large and will often miss certain edge cases.

We looked at utility AI. It is similar to an FSM, except the transitions are handled by which state has the highest fitness for a given situation. The main benefit is that we would not need a separate editor for these; they would work with the tools we already have in place. They also seem simpler to implement.

We decided to go with the utility AI. 

## Implementation

We needed an interface for calling the appropriate functions. 

Alfonso suggested implementing a base class and using virtual functions to implement state-specific behaviour. The main two cons with the base class approach is that entt does not support polymorphism; ```reg.view<AIStateBaseClass>();``` is ill-formed. We cannot do ```reg.view<AIStateDerivedClass>()```, as this view needs to be created in some engine system, while the class could be part of the game project, a project that gets compiled after the engine. There is no way for the engine to ```#include "AIStateDerivedClass.h"``` if this file is located in the game project. Finally, it would not be possible for a virtual script to derive from the base class and override the function, as there is currently no support for deriving from C++ types and there is no easy way to implement this.

I proposed an alternative. I made a drawing to illustrate what I meant.

![](/img/projects/y2/coral/AIDiscussion.jpg)

Entt was designed for DOD and naturally does not like OOP concepts such as inheritance. Instead, each state would be a different component that could be added to an entity. This means our level editor can be used to specify which states the AI can ever be in, to modify the default values of a state and to inspect how the values change at runtime. At the time I was working on an events system that could invoke callbacks for OnCollision events. These events could also be applied to the AI; with an OnAIEvaluate event for example. These events can be bound to a visual script function, which allows designers to create custom behaviour. By using the runtime reflection system, we can also obtain a list of all classes that implement these events.

This implementation can fulfil all the requirements we had in mind. Alfonso agreed and he started working on the system.

## Did we make the right decision?

I had overestimated the simplicity of utility AI. It can be quite tricky to come up with a suitable evaluation for a given state, and it is likely we will run into that struggle more as time goes on. A finite state machine would have similar issues when scaled up but would have ultimately given the user more control. If we had more time, an FSM would have been a better decision, but as it stands, a utility AI implementation will have to do. 
As for our implementation, it is not immediately intuitive for a C++ programmer to *know* that the events exist without reading the documentation, asking for help, or looking at example code. It is not immediately clear that the binding happens in the reflect function. I think the alternative, of having a base class, would have made this more intuitive to brand-new users. We did not consider the increased intuitiveness that the base-class implementation API offered, and this is something we should have considered more. 

We spent time discussing the various options which saved us from running into various issues down the road. Our current implementation was relatively simple and was able to utilise a lot of functionality already in the engine. In the end, I think we made the right decision.