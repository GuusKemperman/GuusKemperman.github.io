---
layout: post
title: "Runtime reflection"
date: 2024-11-05
tags: Coral
---

> *Runtime reflection: the ability to dynamically inspect, access, and modify an object's type information, properties, and methods at runtime, enabling features like serialization, scripting integration, and editor tooling.*

I wrote a custom runtime reflection system for Coral Engine.
It supports both C\++ classes and **'fictional' types**, types that have no C++ equivalent. This allowed for seamless integration between visual scripts and C\++, a feature missing from existing solutions.

 The reflection system is capable of reflecting classes, their constructors, baseclasses, fields and functions. The Reflect function is trivial to implement, and gives the user a chance to customise some behaviour:

```cpp
CE::MetaType CE::Player::Reflect()
{
	MetaType type = { 
			MetaType::T<Player>{}, 
			"Player", 
			MetaType::Ctor<uint32>{}, // Registers a constructor that takes a uint32
			MetaType::Base<ExampleBaseClass> // Registers our baseclass
	};

	// Expose the player to the scripting tool
	type.GetProperties().Add(Props::sIsScriptableTag);

	type.AddField(&Player::Health, "Health")
		.GetProperties()
			.Add(Props::sIsScriptableTag);
			.Add(Props::sIsEditorReadOnlyTag)
			.Add(Props::sNoSerializeTag);

	type.AddFunc(&Player::Respawn, "Respawn")
		.GetProperties()
			.Add(Props::sIsScriptableTag)
			.Add(Props::sCallFromEditorTag);

	BindEvent(type, sOnBeginPlay, &Player::OnBeginPlay);

	ReflectAsComponent<Player>(type);
	return type;
}
```

There is limited **static reflection** and **code generation**. The macro invocation ```REFLECT_AT_START_UP(Player)``` can optionally be added. The codebase is parsed just before compilation, finding all occurences of ```REFLECT_AT_START_UP```. After parsing the codebase, a header is generated that can be used to invoke all the reflect functions at startup. 

The full implementation can be found here on github:
- [include/meta](https://github.com/GuusKemperman/CoralEngine/tree/main-lite/Include/Meta)
- [source/meta](https://github.com/GuusKemperman/CoralEngine/tree/main-lite/Source/Meta)