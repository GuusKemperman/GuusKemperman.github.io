---
layout: project
name: "Lichgate"
---

An action rogue-like where you must grow stronger to vanquish the undead. Lichgate was the first game to come out of [Coral Engine](/projects/coral-engine).

# My Contributions

While I was mostly focused on maintaining and improving Coral Engine throughout this project, I also contributed directly towards the game.

- *[Flowfields](#flowfields)*: Implemented an optimised flowfield algorithm for **AI** navigation.
- *[Procedural Generation Tool](#procedural-generation-tool)*: Developed tools for Level designers for generating endless, **procedural levels**.

# Flowfields

Our game featured many enemies. Rather than calculating thousands of paths, I used flowfields for more optimised pathfinding. 

<div class="video-as-gif-container">
  <video autoplay loop muted playsinline>
    <source src="/img/projects/y2/lichgate/flowfields.mp4" type="video/mp4">
  </video>
</div>

The flowfield moves with the player and is updated asynchronously. The flowfield has multiple level of, allowing the flowfield to extend a greater distance while only being calculated at a lower resolution.

I later made a prototype for **3D flowfields** in **Unreal Engine**, allowing agents to navigate any surface.

<div style="display: flex; justify-content: space-between; align-items: flex-start; gap: 10px; width: 100%;">

  <!-- First Video with Caption -->
  <div style="flex: 1; text-align: center;">
    <div style="position: relative; width: 100%; padding-top: 56.25%; /* 16:9 Aspect Ratio */ overflow: hidden;">
      <video autoplay loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
        <source src="/img/projects/y2/lichgate/SingleSpider.mp4" type="video/mp4">
      </video>
    </div>
    <p style="margin-top: 8px; font-size: 1rem; color: #555;">A single agent using the flowfield to move along any nearby surface towards the target.</p>
  </div>

  <!-- Second Video with Caption -->
  <div style="flex: 1; text-align: center;">
    <div style="position: relative; width: 100%; padding-top: 56.25%; /* 16:9 Aspect Ratio */ overflow: hidden;" onclick="this.querySelector('img').style.filter='none'; this.querySelector('span').style.display='none';">
      <img src="/img/projects/y2/lichgate/FinalSpidersInBucket.gif" alt="Spiders" style="filter: blur(10px); position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
      <span style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: white; background: rgba(0, 0, 0, 0.7); padding: 5px 10px; border-radius: 5px; font-size: 1.2rem; text-align: center;">⚠️ Arachnophobia Warning: Click to view</span>
    </div>
    <p style="margin-top: 8px; font-size: 1rem; color: #555;">A swarm of spiders, climbing along walls and on eachother to reach the target.</p>
  </div>
</div>

# Procedural Generation Tool

The tool worked by using a layering system; each layer can spawn a certain set of objects, and the level designer could manipulate the perlin noise to determine the frequency of the spawned objects.

<div class="video-as-gif-container">
  <video autoplay loop muted playsinline>
    <source src="/img/projects/y2/lichgate/perlin-generation.mp4" type="video/mp4">
  </video>
</div>

The entire system is deterministic during a playthrough, each area will look consistent everytime the player returns.

The tool was used by our level designer, Nikola Tunev, to create the endless world.

<div class="video-as-gif-container">
  <video autoplay loop muted playsinline>
    <source src="/img/projects/y2/lichgate/EndlessTerrain.mp4" type="video/mp4">
  </video>
</div>

The generation of the terrain has been optimised significantly and can keep up with the fastests of players, even with the sheer ridiculousness of the endgame movement speed,
