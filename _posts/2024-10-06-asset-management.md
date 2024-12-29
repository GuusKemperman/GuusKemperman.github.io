---
layout: post
title: "Asset Management"
date: 2024-11-05
tags: Coral
---


I spent a lot of time on the content pipeline, the importing, editing and loading of assets.

## Importer pipeline

The importing pipeline is something I created very early on in the project. The code behind it is robust, yet easy to understand. Leo, a programmer that joined our team, was new to the importing system, but it did not take long for him to get a deep understanding of how the system works. At the end of the block, he even visualised our importer pipeline. The chart shows the steps taken during the importing of a model.

![](/img/projects/y2/coral/ImporterPipeline.png)

The importing produces .asset files, containing the asset in binary format along with some useful metadata. Importing large asset packs took quite long though, which I fixed by asynchronously importing the files. The importing became around 8x faster AND the editor remains responsive! I even made it possible to reimport your assets and watch them update, live in the editor.

![](/img/projects/y2/coral/ImportingWorkflow.gif)

## Editing pipeline

I have created a universal system for editing assets at runtime.

![](/img/projects/y2/coral/ContentEditing.png)

I made sure that the user is not live-editing the original instance of the asset, as it could be dangerous to work on assets that are referenced throughout the engine. Instead, the user works on a copy of the original asset. The changes are only applied to the original asset when saving. This is why I made sure when designing the API for the AssetManager to only distribute immutable handles to assets; if you want to edit an asset, you will have to go through the proper channels.

Our engine supports an **undo** feature for all edits, for all assets. I implemented this by periodically saving the asset to memory to check for changes, and restoring the asset to that state when requested.

I wanted to make editing assets at runtime be *just* as safe as asset files changing while the engine is closed. When finally saving an asset to a file, the editor does a soft restart behind the scenes. All the changes present in each AssetEditor are carried over, to ensure your progress does not get erased and the user is still able to undo their actions. 

## Moving and Renaming

To ensure references to assets remain intact, I added the option to safely rename and move assets. When you rename or move a file through the editor, a redirect file is left in place.  

## Packager

In order to reduce load-times for our players, I wrote a packager. The packager removes unneeded information from the metadata and combines all assets into one file. While removing the metadata slightly reduces the size of assets, the main benefit is that our shipped games do not have to iterate over the files in the asset directory at start-up.

