---
layout: project
name: "Spider Sling"
---

I was inspired by the game Webbed for my current idea. You play as a spider to swing around an infinite world, with a rising platform. This forces the player to constantly swing to higher ground. To further hinder the player, there are fireflies attacking them with flaming projectiles. The player can defend themself with a deadly laser fired from their eyes.

The focus of this project was on creating and optimising **2D raytracing**. optimisation, in the end I was able to easily render a world with hundred of thousands of objects. The bulk of the rendering work through **OpenGl's compute shaders**. This turned out to be a great success, I was able to report something new that I'd learned almost every week. I learned my way around new tools such as **RenderDoc**.

# Features

- *[Raytracing](#raytracing)*: A summary of features, with a focus on an especially interesting optimisation.
- *[Editor](#editor)*: A custom level-editor designed to make my life easier.
- *[BVH](#bvh)*: The acceleration structure used for raytracing and physics.

# Raytracing

The game was made in my own framework, whose selling feature is the 2D raytracing. The raytracing included:

* Soft shadows
* Area lights
* Anti-aliasing
* Reflections
* Denoising
* And much more!

## Shadow grid

Raytracing is *slow*, because you have to do traces for each pixel. Or do we? I want to share one of the interesting methods I've used to optimise the raytracing.

Imagine you have a small square on the screen, and you check if each corner of that square has a direct line of sight to the light. If all of the corners can 'see' the light, it is likely the whole square will be lit, and similarly, if none of the corners can 'see' the light, the square is most likely fully occluded; we don't have to do any raytracing. Only if the results from each corner are different is the square partially occluded will we have to perform raytracing for each pixel in the square.

I implemented a shadow-grid, which recursively divides the screen up into squares; 

- Fully lit squares require no raytracing but still need to be lit based on the distance to the light.
- Fully occluded squares are fully inside the umbra are discarded completely, since they would be completely black anyways.
- Partially lit squares represent squares inside the penumbra, they require full raytracing.

| ![](/img/projects/y1/blockc/NoShadowGrid.jpg) | ![](/img/projects/y1/blockc/shadow-grid.gif)| ![](/img/projects/y1/blockc/WithShadowGrid.jpg) |
|---|---|---|
| without shadow grid, 50FPS | Only blue squares require raytracing | With shadowgrid, 280FPS! |

The same principle can be applied to reflections. Only squares whose corners can't agree on whether they can 'see' the reflected light need to be raytraced.


<table><thead>
  <tr>
    <td><img src="/img/projects/y1/blockc/reflection-grid.gif"></td>
    <td><img src="/img/projects/y1/blockc/large.gif"></td>
  </tr></thead>
</table>


This grid was inspired by the screen-space grid showed in the Heaven7 demo. It involved a lot of math to get right, especially when using area lights, but the performance gains were massive.

![](/img/projects/y1/blockc/performance.gif)

The performance now well exceeded requirements; I was able to support 80.000 objects **on screen** at 140FPS! 

This optimisation was definitely a tradeoff, as it has one major downside. You cannot have objects that are smaller than the initial size of the square or really thin walls; there are some edge cases where each corner of a square will 'miss' this object, causing it to be fully lit when it should be partially occluded. In the above gif, you can see that objects get missed when fully zoomed out; the object is smaller than the size of the squares. 

# Editor

I wanted to take a break from lighting for a bit and decided spending some time on creating an editor.

![](/img/projects/y1/blockc/Editor.gif) 

Features:
- Spawn/destroy objects.
- Modify object through info window, e.g., change the light's colour.
- Move objects by dragging them in the sceneview.
- Parent objects through the scene hierarchy.
- Serialization, for which I could reuse the serialization system from last block.

# BVH

Performance absolutely tanked when there were many occluders on screen. The BVH was meant to solve this.

I followed [Jacco's BVH series](https://jacco.ompf2.com/2022/04/13/how-to-build-a-bvh-part-1-basics/) on how to implement the BVH. There were some key differences between the guide and what I required; the guide is 3D, and only supports triangles, while I hope to support three different 2D shapes. To keep the cache happy and the data aligned, I needed a node size of 32 bytes. Luckily, this just about fits it we are clever with how we store data. I keep track of the amount of AABBs and Circles, but extrapolate the number of Polygons based on the number of total objects. This way I can use the mTotalNumOfObjects != 0 as an indicator that this node is a leaf.

```cpp
struct Node
{
	TransformedAABB mBoundingBox{};
	uint32 mStartIndex{};
	uint32 mTotalNumOfObjects{}; 
	uint32 mNumOfAABBS{};
	uint32 mNumOfCircles{};
};
static_assert(sizeof(Node) == 32);

``` 

After my initial implementation, the FPS rose from <1 to 130FPS with reflections, 200FPS without reflections!
