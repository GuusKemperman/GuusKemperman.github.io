---
layout: project
name: "Umbra Mortis"
---

Umbra Mortis is a 1-3 player coop FPS shooter, where you fight hordes of zombies in the streets of Venice. 

It was amazing to get experience with Unreal's networking and multiplayer programming systems, and all the challenges that came with them. I was lucky enough to be able to work together in an amazing team of 22 developers, collaborating closely and achieving our goal of a successful Steam release.

# My Contributions

- Created development tools, such as the objectives system, cheat menu, analytics, automated crash reports & improved logging.
- Gameplay features, such as the homing bell, an AI director, spectating, as well as the functionality behind many widgets and triggerable events in the level.
- Optimising enemy navigation, created efficient local avoidance for 3D environments.
- Bug fixing, mainly replication related issues, leading to an incredibly stable build on release.

This page is still under construction, with more details about my contributions coming soon. In the meantime, feel free to check out our game on Steam!

<div style="text-align: center;">
  <iframe 
    src="https://store.steampowered.com/widget/3365870/?t=Rise%20children%20of%20the%20almighty%20and%20be%20guided%20to%20your%20salvation.%20You%20must%20trek%20a%20path%20of%20mud%20and%20blood%2C%20but%20if%20you%20walk%20it%20together%2C%20you%20will%20surely%20have%20enough%20strength%20to%20tear%20through%20these%20beasts.%20Grab%20two%20friends%20and%20shoot%20zombies%20in%20this%20Venice%20carnival%20inspired%20shooter." 
    frameborder="0" 
    width="646" 
    height="190">
  </iframe>
</div>

# Procedurally animated enemies

In the early days of prototyping our game, I added a feature for supporting thousands of procedurally animated enemies, with very minimal bandwith overhead, using flowfields.

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

Since the spiders follow a fixed flow-field, *most* of the time, they do not care about the player's position; they just follow the flow field. In order to reduce bandwidth usage, the player positions would be collected on the server at small intervals, which would be sent back to the client with an instruction to generate a new flowfield  using the more up-to-date information, and to swap it inat a specific time. This led to the flowfields being consistently synced across the clients: thousands of spiders synced at the cost of sending only a handfull of bytes periodically.

There is a caveat to this: Spiders consistently move to the position the player was at *just a second ago*. They are acting on slightly out of date information. My solution? Slowly change their behaviour to have them move towards directly the player once they start getting very close to the player. The spiders are marked as 'dirty', and their positions are now temporarily fully replicated.

In the end, this feature was scrapped due to design and artist restrictions for our game. I shared the prototype with another team, who used it as a starting point for their steam release [Oh Bugger](https://store.steampowered.com/app/3365900/Oh_Bugger/).