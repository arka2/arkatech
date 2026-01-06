---
layout: post
title: "Metallic Gold Line Art Shader"
author: "Arka Tu"
categories: shaders
tags: [shaders]
image: gif_01.gif
---
*Character designs by Charlotte Pang \| UI by Valerie Caña*

Shards Between Us is an isometric game made entirely with 2D assets. With the art inspired by Art Nouveau and stained glass, I wanted to show metallic line art.

The game is being developed in Unity, and I used ShaderGraph for this shader.

## Details
---

![Screenshot of Unity inspector, showing exposed variables such as highlight color and shadow color.]({{ site.github.url }}/assets/img/inspector_01.png)

I exposed the shine color, speed, width, opacity, and angle. I added a variable to adjust the color of the line art as needed. I also exposed a drop shadow color for the line art and its distance.

Originally, the shader uses a normal map for 2D lighting and a separate line art map, but I realized we only needed the alpha channel from the line art. I wrote a Python script to replace the normal map's alpha channel with the corresponding line art's. I then saved the new image as a DDS file, so the individual channels would remain intact and still be usable in the shader.

```python
...
# Do the files exist?
if os.path.isfile(normalFile) and os.path.isfile(alpha):
    with Image(filename=normalFile) as img:
        with Image(filename=alpha) as alphaImg:

            # Make sure the image's alpha channel is enabled
            if img.alpha_channel == False:
                img.alpha_channel = True

            # Copy alpha channel from line art to normal map
            img.composite(image=alphaImg, operator='copy_alpha')

            # Convert to DDS
            img.compression = "dxt5"
            dds_file = f"{imageName}_{normalSuffix}.dds"
            
            # Save as DDS
            img.save(filename=dds_file)
else:
    print('invalid files')
    return -1
...
```

## The Problem
---

The first pass of our game had all of the art as sprites with no shaders. All the lighting was painted in as well. I wanted the line art to look like gold, so I baked in the metallic shine into the sprite. Unfortunately, this made the level difficult to read. When we started on the second pass of Shards, I wanted to update the art to be more flexible and dynamic. I added normal maps to the tiles and, more importantly, started working on a line art shader.

![Screenshot showing painted 2D assets with a gold metallic shine baked into the texture.](https://arkatu.com/arkatech/assets/img/screenshot_01.png)

![Another screenshot showing painted 2D assets with a gold metallic shine baked into the texture, shown across tiles.](https://arkatu.com/arkatech/assets/img/screenshot_02.png)

## The Solution
---

When we started development on Shards Between Us, I discovered that I might be able to implement a shine across the line art by using shaders. I watched YouTube tutorials and scoured the Unity ShaderGraph documentation as I experimented in-engine. I ended up with a shader that takes a sprite's line art, colors it gold, and adds a subtle drop shader under the line art.

![Screenshot of ShaderGraph, showing the nodes that calculate the drop shadow under the line art.](https://arkatu.com/arkatech/assets/img/nodes_02.png)

![Screenshot of ShaderGraph, showing the nodes that combine the metallic glint and allow dynamic line color.](https://arkatu.com/arkatech/assets/img/nodes_03.png)

I originally added a shine across the entire screen's line art, but we decided to limit the shine to interactable objects. We wanted this to help the player figure out what they could interact with. Originally, the shine one each object would sync.

![Two characters stand on blocks surrounded by water. A gold metallic glint slowly crosses all of the line art on the screen.](https://arkatu.com/arkatech/assets/gif/gif_03.gif)

This led me to write a short script to get a different time interval for the objects rendered. With this, the shine looks more natural.

```c#
...
private void SetRandomTimeOffset() {

    float randomValue = UnityEngine.Random.value * ScaleRandom;
    _propertyBlock.SetFloat(RandomTimeOffsett, randomValue);
    _propertyBlock.SetTexture("_MainTex", tex);

    _renderer.SetPropertyBlock(_propertyBlock);
}
...
```
![Screenshot of ShaderGraph, showing the nodes used to calculate the random time offset for a game object.](https://arkatu.com/arkatech/assets/img/nodes_01.png)

![Two characters stand on blocks surrounded by vegetation. A shard of glass and a tall, broken door glint periodically, showing the finished shader.](https://arkatu.com/arkatech/assets/gif/gif_02.gif)
