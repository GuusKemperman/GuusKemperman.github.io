---
layout: project
name: "Turbo Tails"
---

This award-winning game was created by a talented team of fifteen individuals over eight weeks. It received the Best Y1 Game 2023 award from industry professionals and the best tech award from BUAS staff. During the project, I honed my skills in using the scrum methodology, collaborating in a multi-disciplinary team, and developing with Unreal Engine. My key responsibilities were AI, the weapons, and UI development.

My communication with the team in general went well, especially for the first time working in a gamedev team. I gained a lot of positive feedback during peer reviews regarding my reliability and helpfulness.

# My contributions

- *[AI](#ai)*: Computer controlled opponents
- *[Weapons](#weapons)*: Created a tool for weapon creation, included custom physics.
- *[UI](#weapons)*: Created navigation system for multiple players, along with various menus and HUD elements.

# AI

I realised early on that it would be difficult to debug and test features for a local multiplayer game; you would constantly have to task a team-member to help you test weapon interactions. I pushed for adding simple AI, even just something that could only follow a track.

![](/img/projects/y1/blockd/ai-debug.gif)

*The AI used for debugging in the early weeks of development*

The AI has been a vital part of the testing process and significantly improved the development and planning stage; particularly the camera movement and weapons testing for early prototyping. In the second sprint I upgraded the AI to be more than just a debugging tool, but an actual part of the player experience.

The AI work using steering behaviours. Depending on the state of the AI, they combine their desire to stay on the track, their desire to not bump into others, their desire to **jump off of ramps** and their desire to wander randomly. They steer towards this combined desired velocity.

The weapons are each quite different. To make the system as modular as possible, I decided to use the **smart objects** approach; the weapons themselves evaluate how useful it would be to fire in a given scenario, the character chooses to fire the weapon based on the weapons score.

<div class="video-as-gif-container">
  <video autoplay loop muted playsinline>
    <source src="/img/projects/y1/blockd/ai-showcase.mp4" type="video/mp4">
  </video>
</div>

*The pink car is the only human character. The AI are able to use weapons, stay on track and take shortcuts*

The AI were frequently re-balanced as gameplay evolved. I ensure that the behaviour could be easily adjusted through a couple of simple variables. This opened up the doors later for an interesting feature that quickly goes unnoticed: each mouse has a **distinct personality**! Some mice have a more aggressive playstyle, they'll use weapons quicker and will take the ramp shortcut whenever they can. Others just got their drivers license, and still strugle with driving in a straight line. 

## Weapons

I created a weapons system, the basis for others to start creating weapons that can be picked up and used by the players. I collaborated with the designers; I listened to their needs and adjusted the weapons system accordingly, further improving the quality of my contribution. I made the necessary documentation to help them further.

![](/img/projects/y1/blockd/weapons-and-force.gif)

*Custom explosions for more consistency between flipping and pushing objects*

Unreal Physics are great; but they didn't match the arcade-like feel that we were going for. I added a system for flipping and pushing the car, making the response to bumping into eachother and explosions much more consistent and easy to adjust by designers. 

# UI

I added support for navigating the UI specifically for local multiplayer. In some menus, every player was able to control the same navigation, such as the main menu. In other menus, each player controlled their own navigation, for example the player-select menu. I created a simple tool that allowed us to easily switch between these two behaviours and integrated it with the existing Unreal UI system. 

I created the main menu, the pause menu and the splash screen. Each menu included animations, navigatable buttons. They supported a varying number of players, and scaled properly at different resolutions.

<div class="video-as-gif-container">
  <video autoplay loop muted playsinline>
    <source src="/img/projects/y1/blockd/TurboUI.mp4" type="video/mp4">
  </video>
</div>

I contributed to the HUD. I created UI for aiming the airstrikes. I implemented colourblind support by adding hovering numbers that follow each car.

![](/img/projects/y1/blockd/color-blind.png)


