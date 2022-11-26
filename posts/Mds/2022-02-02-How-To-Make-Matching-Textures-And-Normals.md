---
aliases:
- /baking/blender/game/gimp/map/normal/2022/02/02/How-To-Make-Matching-Textures-And-Normals
categories:
- gimp
- blender
- baking
- normal
- map
- game
date: '2022-02-02'
description: Featuring Blender and Gimp!
layout: post
title: How Do You Create a Normal Map?
toc: false

---

## How We Got Here
Looking around the Internet, it ended up being much harder to find the information about how to make a Custom Normal Map then I though it should be. After finally figuring this out, I thought I'd formalize/share it here so that it doesn't get lost among everything else.

## Where We're Going
We'll go over the how to do this with a simple example: dirt.
First you'll want to start blender up and clear the scene. Drop in a new plane and make it whatever size you like; I scaled it by 2x so if you're trying to just copy my examples then simply do that.
<img src="{{ site.baseurl }}/images/blender-bake/starter-plan.png" width="500" height="400">
Since we're just working on making a material to import into a game, we don't really need anything else. So, we're moving on to making the texture:
<img src="{{ site.baseurl }}/images/blender-bake/switch-to-shading.png" width="500" height="400">

Again, this is just a basic example so we'll create something like this:
<img src="{{ site.baseurl }}/images/blender-bake/basic-ground-material.png" width="500" height="400">
Not bad, not complicated but will show us the effect we're after; feel free to copy the settings if you're following along.
Now add an **Image Texture Node** to the graph but don't attach it to anything:
<img src="{{ site.baseurl }}/images/blender-bake/select-new-image-texture.png" width="500" height="400">

Create a new image and call it what your target material is about. In my case, I'm just making dirt so we're calling it *BasicDirt*:
<img src="{{ site.baseurl }}/images/blender-bake/fill-out-new-image-texture.png" width="500" height="400">
Now we're going to do something called **Baking**. So, what is *Baking* actually? What we're doing is saving information to the texture so that it doesn't have to be re-calculated. *Eevee* - the default engine in blender - doesn't support baking so you'll need to switch over to *Cycles* for this to work. Once that is done then go ahead and click **Bake** and it should work!
<img src="{{ site.baseurl }}/images/blender-bake/cycles-bake-to-image.png" width="500" height="400">
You should now see the image show up in the *Image Editor* in the UI. You'll want to save this to an acutal file for use in the Game Engine - or for other uses like making a normal map for it.
<img src="{{ site.baseurl }}/images/blender-bake/save-the-new-material.png" width="500" height="400">
<img src="{{ site.baseurl }}/images/blender-bake/BasicDirt.png" width="500" height="400">

## The Next Stop
Ok, now that you have the texture go ahead and pull this into your photo editing software. I'm going to do this with Gimp so to follow along you're going to need it. Otherwise, you should just be able to look up "Making a Normal Map in Photoshop" and it should be a simple option. I spent quite a bit of time trying to make the Normal Map in Blender and I couldn't get it working; if someone did figure this out then please let me know. But until then, you'll want pull your image into Gimp first. Once that is done, you'll select **Filters** > **Generic** > **Normal Maps{{ site.baseurl }}.** and it will present you with the ability to set how harsh the effect is:
<img src="{{ site.baseurl }}/images/blender-bake/create-the-normal-map.png" width="500" height="400">
... and that scale is not the default. Go ahead and play around with that number to get the effect you want. Depending on your Game Engine, save it with the proper nomenclature for when you pull it in.
<img src="{{ site.baseurl }}/images/blender-bake/T_BasicDirt_N.png" width="500" height="400">

Get your Blender project back open and hop into the *Shading* Pane once more. Add a new **Image Texture Node** and insert the new Normal map you created. Once added the image into the *Image Texture Node*, you'll want to switch over to non-color:
<img src="{{ site.baseurl }}/images/blender-bake/change-from-color-to-normal.png" width="500" height="400">

And, that's how you Make a Normal Map, and add it into blender. You can improve it some using other options like adding a *Bump Node* between it.
<img src="{{ site.baseurl }}/images/blender-bake/add-the-normal-map-to-the-texture.png" width="500" height="400">