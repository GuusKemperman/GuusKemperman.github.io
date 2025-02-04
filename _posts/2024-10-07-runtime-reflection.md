---
layout: post
title: "Runtime reflection"
date: 2024-11-05
tags: Coral
---

> *Runtime reflection: the ability to dynamically inspect, access, and modify an object's type information, properties, and methods at runtime, enabling features like serialization, scripting integration, and editor tooling.*

I wrote a custom runtime reflection system for Coral Engine. The reflection system is capable of reflecting classes (both C\++ classes and [fictional types](#fictional-types)), their constructors, baseclasses, fields and functions.

## Reflecting API

Classes can implement a Reflect function. The Reflect function is trivial to implement, and gives the user a chance to customise some behaviour:

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

There is limited **static reflection** and **code generation**. By default, classes are reflected when their MetaType is requested. The macro invocation ```REFLECT_AT_START_UP(ClassName)``` can be added to indicate you want the class to be reflected right from the start. The codebase is parsed just before compilation, finding all occurences of ```REFLECT_AT_START_UP```. After parsing the codebase, a header is generated that can be used to invoke all the reflect functions at startup. 

## Usages

By reflecting types at start-up, you can iterate over generic groups of types; for example all types that inherit from System: 

```cpp
const MetaType& systemType = MetaManager::Get().GetType<System>();

for (const MetaType& derivedType : systemType.EachDerivedClass())
{
	AddSystem(derivedType.Construct().Get());
}
```

The runtime reflection system allows for **improved modularity** and **reduces tight coupling**. All reflected classes that inherit from System are registered, without the writer of those systems having to worry about how, where and when they need to be registered. Both system types that originate from the engine and system types that originate from the game code are stored in the runtime reflection system, making it a fantastic way for the engine to gain access to game specific code through a layer of abstraction.

The engine often needs to write generic code, for example to inspecting components, displaying their details in the level editor. It's not always feasible to accomplish this through templates, as this often requires the full type definition, and even so, iterating over member fields is not supported in C++. I wrote the runtime reflection system to resolve this problem, to be able to write generic code dealing with abstract types. Take this example code snippet used for inspecting components:

```cpp
MetaAny component = world.Get(componentClass.GetTypeId(), entity);

for (const MetaField& field : componentClass.EachField())
{
	if (field.GetProperties().Has(Props::sNoInspectTag))
	{
		continue;
	}

	const MetaType& fieldType = field.GetType();
	const MetaAny currentValue = field.CanGetConstRef() ? 
		field.GetConstRef(component) : 
		field.Get(component);

	MetaAny newValue = fieldType.Construct(currentValue).Get();

    fieldType.CallFunction(sShowInspectUIFuncName, field.GetName(), newValue);

	const bool areValuesEqual = *fieldType.CallFunction(OperatorType::equal, 
			currentValue, 
			newValue).Get().As<bool>();

	if (!areValuesEqual)
	{
		field.Set(component, newValue);
	}
}

for (const MetaFunc& func : componentClass.EachFunc())
{
	if (!func.GetProperties().Has(Props::sCallFromEditorTag))
	{
		continue;
	}

	if (ImGui::Button(func.GetName()))
	{
		func(component, world);
	}
}
```

## Fictional types

The engine uses the functionality that it needs, e.g., getting and setting fields, without having to use any templates or worry about the implementation details of these classes. The best part? Some of these MetaTypes may not even represent C\++ classes; they represent **'fictional' types**, types that do not have a C++ equivalent! The unified interface for C\++ and fictional types allowed [visual scripts](/blog/visual-scripting) to be seamlessly integrated with the rest of the engine, something that would not have been possible through existing runtime reflection libraries.

In order to create a interface for both C\++ and fictional types, I designed fictional types to closely mimick how C\++ classes work. 
A class in C\++ is *mostly* just a buffer, with different offsets representing different subobjects inside that object.

![](/img/projects/y2/coral/PlayerLayout.png)

I used the same principle to represent fictional types. To, for example, construct an instance of a fictional type, I allocate a buffer large enough to hold all the member fields (taking into account padding requirements), and construct each member field in place at increasingly higher offsets.

## Conclusion

The runtime reflection system has by far been the most defining system for the success of Coral Engine. It heavily influenced how code was structured, it enabled modular, generic and clean code. The visual scripting, one of the most useful tools, would not have been possible without it.

The full implementation can be found here on github:
- [include/meta](https://github.com/GuusKemperman/CoralEngine/tree/main-lite/Include/Meta)
- [source/meta](https://github.com/GuusKemperman/CoralEngine/tree/main-lite/Source/Meta)