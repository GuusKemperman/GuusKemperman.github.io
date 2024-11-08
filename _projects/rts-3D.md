---
layout: project
name: "RTS 3D"
---


## Significant achievements

I am proud of my serialization system, especially since it seemed like the greatest challenge to me at the start of this block. I was able to make use of factories for each entity type that I wanted to serialize, and entityIds to preserve relations between them.

I've never built for anything outside of Windows before. I'm quite happy that I got my project to work on both Windows and the Raspberry.

I was really proud of the procedural units and their behavior. I designed the turret's and unit's behavior to be completely independent from one another, which allows me to have any number of turrets. I was also surprised that the planes worked so well, without me having to change anything. They can bump into each other and crash, and then slowly recover.


## Significant challenges

I found it difficult to efficiently query for objects in a certain area using Bullet. I have units that only need to check for units in a small radius around them, but only every 0.2 seconds and only if their current state requires it. I tried a lot of approaches, but all with their flaws. The sweep test does not use groups/masks, inserting objects and then removing them will only do a broadphase check, pair caching ghost objects make checks every frame, even when I don't need them to. I ended up using a contact test, but this means bullet will keep making unnecessary checks after finding the first contact point between two objects, and it requires me to filter out duplicates.

I struggled with implementing a loading screen. I gave up on threading, something I regret. I was almost finished implementing it, but then ran into some OpenGL issues. In hindsight, I should've spent more time on it: I could've looked into using multiple contexts, or have a few loading threads doing all the heavy lifting and only using the main thread for making OpenGL calls and drawing the loading screen. My current solution works, but it requires tasks to be subdivisible into smaller tasks to keep a high and consistent fps for the animated gif on the loading screen.

I struggled with creating a high quality worklog, especially my approach in the first four weeks was not ideal. I write about this in my reflection of the worklog goal. I have thought about how to improve and reflected on it, both on that slide as well and on the slide after this one. 


## Lessons learned

I've learned a lot about OpenGL. I ran into some issues early on which were frustrating to fix; you don't get a lot of debug information. This became easier when I made a pc build, for which I could use RenderDoc. I've learned a lot about the very basics of rendering.  

I've learned about the existence of Imgui and how to use it. It is incredibly extensive and reliable, saving me a lot of time. Being able to easily change variables at runtime was also incredibly helpful, especially with the Pi's compilation time.

I've learned how to develop games for the Raspberry Pi, and will be more comfortable developing games for consoles in the future.



