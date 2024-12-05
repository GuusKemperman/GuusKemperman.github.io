---
layout: post
title: "Content Pipeline"
date: 2024-11-05
tags: Coral
---

I spent a lot of time on the content pipeline, the importing, editing and loading of assets.

## Importer pipeline

The importing pipeline is something I created during block A. The code behind it is robust, yet easy to understand. Leo was new to the importing system at the start of the block, but it did not take long for him to get a deep understanding of how the system works, to add onto it by importing skinned meshes and to make changes to existing importers. At the end of the block, he visualised our importer pipeline. The chart shows the steps taken during the importing of a model.

![](/img/projects/y2/coral/ImporterPipeline.png)

In sprint four part of our sprint goal was to refine our engine's toolchains and workflows. The importing previously happened 'automatically' when changes were detected. This gave the user very little control, and it often led to unneeded reimporting and overwritten changes. When importing large files, or asset packs containing many models, the engine would freeze for up to several minutes. It became apparent that this process required optimisation. 

I had always found multi-threading to be too bug-prone for it to be worth it. Often the computation needed for collecting the jobs and creating the threads led to insignificant performance improvements. In this block, I learned about and applied asynchronous operations; a thread gets launched to perform an expensive task, while the main thread continuously updates the engine, occasionally checking if the result is ready yet. It makes the application more responsive and leads to performance improvements that would otherwise not be possible. The use of multi-threading makes more sense to me now.

Asynchronously processing the files has led to significant performance improvements, it is around 8x faster. The system duly informs the user that it is performing work.

![](/img/projects/y2/coral/ImportingWindow.gif)

The importing workflow became quite streamlined, it was even possible to reimport your assets and watch them update, live in the editor.

![](/img/projects/y2/coral/ImportingWorkflow.gif)

## Editing pipeline

We have created a universal system for editing assets at runtime.

![](/img/projects/y2/coral/ContentEditing.png)

Classes can derive from AssetEditorSystem. The AssetEditorSystem owns the copy of your asset, it is responsible for saving assets to a file and provides some functionality for undo/redo, but more on that later.

We are not live-editing the original instance of the asset, as it could be dangerous to work on assets that are referenced throughout the engine. We instead work on a copy of the original asset. We make our changes and only apply them to the original asset when saving. This is why we only distribute const handles towards the users; if you want to edit an asset, you will have to go through the proper channels.

When finally saving an asset to a file, the editor does a soft restart, making sure to save any unsaved changes to memory first. The stack of changes present in each asseteditor is carried over, to ensure your progress does not get erased.

### Did we make the right decision?

I am quite happy with the asset editing system. It abstracts a lot of the complex functionality away from most programmers, it is simple to add your own assets to it, yet offers plenty of features.

The only regret I have is the way I decided to give out references to assets to the rest of the engine. Currently, we have ```WeakAsset```, which are handles for a possible unloaded asset. But for our actually loaded-in assets, we use ```std::shared_ptr```. This is not only inconsistent, but it also means that you sometimes get more information from the ```WeakAsset``` (the file it was imported from, the version, etc.) than from the loaded-in asset. We also wanted to make the change that assets only get loaded in when the handle is dereferenced, but this could not be achieved with the simple ```std::shared_ptr``` we are using now. The API would have been more consistent if we created our own AssetHandle for loaded-in assets. 

## Undo

Our engine supports an 'undo' feature for all assets. A simple feature, but complicated to implement!

![](..//img/projects/y2/coral/ParentingAndSimpleDoUndo.gif)
*Transform components can have parents and a variable number of children. Each action can be undone using CTRL_Z or through the undo button.*

![](..//img/projects/y2/coral/DestructiveUndoRedo.gif)
*Even destroyed components and entities can be brought back in the exact same state*

In the first iteration, I implemented this using the command pattern. Each edit is defined using a Do and Undo function. For most edits this is trivial; if we move an object to X, we can just as easily move it back to Y. But for destructive edits, this is trickier; if we destroy a component, we no longer have the information necessary to bring it back. It can be resolved by serialising the component before destroying it, but it's not ideal. There are a wide variety of different actions the user can do and undo, and it can be both tedious and error-prone to maintain the different edge cases.

I decided to switch to the memento pattern, where you save different versions of your asset to memory in order to restore them later. Because we are using the memento pattern, the undo system is universal; if you are able to save your asset to a stream, your asset editor will have undo functionality.

[//]: # (TODO: Add more details on memento)

[//]: # (TODO: Add asset renaming)

# Conclusion

Our content pipeline is a system that was carried over from my previous block and has been iterated on and refined many times. The content pipeline is clearly explained using visual diagrams with further elaboration where necessary. Artists and designers are able to import and edit assets as needed, while the code behind it is robust and scalable.