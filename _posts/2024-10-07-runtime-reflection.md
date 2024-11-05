---
layout: post
title: "Runtime reflection"
date: 2024-11-05
tags: Coral
---

# Runtime reflection

Types can be easily reflected and exposed to the scripting tool. The reflection system is capable of reflecting every class, including their constructor, baseclasses, private fields and functions.

Let's say you have this imaginary class:

```cpp
namespace CE
{
	class ParticleEmitterComponent
		: public ExampleBaseClass
	{
	public:
		ParticleEmitterComponent(uint32 numOfParticles);

		// Events!
		void OnBeginPlay(World& world, entt::entity owner);

		void PlayFromStart(bool destroyExistingParticles);

		glm::vec3 mMinInitialLocalPosition{};

		// Why is this a float you might ask?
		// If you want to, for example, spawn .5f particles per frame, you can't do anything the first frame, as you can't spawn half a particle.
		// But the next frame, two halves make one whole.
		float mNumOfParticlesToSpawnNextFrame{};

		/// ... More data members

	private:
		friend ReflectAccess;
		static MetaType Reflect();
		REFLECT_AT_START_UP(ParticleEmitterComponent);
	};
}
```

You can add a ```Reflect``` function. The REFLECT_AT_START_UP macro informs the engine of it's existence when the program initializes. The Reflect function is trivial to implement, and gives the user a chance to customise some behaviour by adjusting properties:

```cpp

CE::MetaType CE::ParticleEmitterComponent::Reflect()
{
	MetaType type = { 
			MetaType::T<ParticleEmitterComponent>{}, 
			"ParticleEmitterComponent", 
			MetaType::Ctor<uint32>{}, // Registers our constructor
			MetaType::Base<ExampleBaseClass> // Registers our baseclass
	};

	type.GetProperties().Add(Props::sIsScriptableTag);

	type.AddField(&ParticleEmitterComponent::mDestroyOnFinish, "mDestroyOnFinish")
		.GetProperties()
			.Add(Props::sIsScriptableTag);

	type.AddField(&ParticleEmitterComponent::mNumOfParticlesToSpawnNextFrame, "mNumOfParticlesToSpawnNextFrame")
		.GetProperties()
			.Add(Props::sIsEditorReadOnlyTag)
			.Add(Props::sNoSerializeTag);

	type.AddFunc(&ParticleEmitterComponent::PlayFromStart, "PlayFromStart")
		.GetProperties()
			.Add(Props::sIsScriptableTag)
			.Add(Props::sCallFromEditorTag);

	BindEvent(type, sOnBeginPlay, &ParticleEmitterComponent::OnBeginPlay);

	ReflectParticleComponentType<ParticleEmitterComponent>(type);
	return type;
}
```

No static reflection or code-generating tools, everything is pure C++ and almost entirely macro-free. This makes the tool easy to use by other programmers.

![](/img/projects/y2/coral/W8_ReflectedCPPInScriptEditor.png)

*The reflected fields and functions, as seen in the script editor*

### Limits

Function arguments are only allowed in limited forms. Parameters can only be of the form Value, Ref, ConstRef, Ptr, ConstPtr, or RValue. A pointer to a pointer for example is not supported. If your codebase has a lot of pointers to pointers or references to pointers, this runtime reflection system may not be the one for you.

### Strengths

The best part of our runtime reflection system? It supports 'fictional' types, types that have no C++ equivalent. This helped greatly with implementing the next feature, Visual Scripting!

[//]: # (TODO: This system is really impressive, give more content)
