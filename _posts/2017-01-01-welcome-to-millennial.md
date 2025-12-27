---
layout: post
title: "Metallic Gold Line Art Shader"
author: "Arka Tu"
categories: documentation
tags: [documentation]
image: gif_01.gif
---
*Character designs by Charlotte Pang \| UI by Valerie Caña*

Shards Between Us is an isometric game made entirely with 2D assets. With the art inspired by Art Nouveau and stained glass, I wanted to show metallic line art.

The game is being developed in Unity, and I used ShaderGraph for this shader.

## Demo

![A small sprout character, Glas, stands between flowering rocks. The line art for each of the rocks flashes gold periodically.](https://arkatu.com/arkatech/assets/gif/gif_02.gif)

## Details

![Alt text]({{ site.github.url }}/assets/img/inspector_01.png)

I exposed the shine color, speed, width, opacity, and angle. I added a variable to adjust the color of the line art as needed. I also exposed a drop shadow color for the line art and its distance.

The shader uses a normal map for 2D lighting and a separate line art map. I WANT TO COMBINE THE NORMAL MAP AND THE LINE ART MAP TO ONLY HAVE ONE EXTRA TEXTURE. The normal map's alpha channel contains the line art alpha.

## The Problem

![Alt Text](https://arkatu.com/arkatech/assets/img/screenshot_01.png)

![Alt Text](https://arkatu.com/arkatech/assets/img/screenshot_02.png)

The first pass of our game had all of the art as sprites with no shaders. All the lighting was painted in as well. I wanted the line art to look like gold, so I baked in the metallic shine into the sprite. Unfortunately, this made the level difficult to read. When we started on the second pass of Shards, I wanted to update the art to be more flexible and dynamic. I added normal maps to the tiles and, more importantly, started working on a line art shader.

## The Solution

When we started development on Shards Between Us, I discovered that I might be able to implement a shine across the line art by using shaders. I watched YouTube tutorials and scoured the Unity ShaderGraph documentation as I experimented in-engine. I ended up with a shader that takes a sprite's line art, colors it gold, and adds a subtle drop shader under the line art.

Screenshot of ShaderGraph?

I originally added a shine across the entire screen's line art, but we decided to limit the shine to interactable objects. We wanted this to help the player figure out what they could interact with. Originally, the shine one each object would sync.

This led me to write a short script to get a different time interval for the objects rendered. With this, the shine looks more natural.

Code Block

Picture

Gif of finished shader

## Getting Started

[Getting Started]({{ site.github.url }}{% post_url 2016-10-10-getting-started %}): getting started with installing Millennial, whether you are completely new to using Jekyll, or simply just migrating to a new Jekyll theme.

## Example Content

[Text and Formatting]({{ site.github.url }}{% post_url 2016-09-09-text-formatting %})
