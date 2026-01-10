---
layout: post
title: "Rendering with OpenGL"
author: "Arka Tu"
categories: pipeline
tags: [pipeline]
image: opengl_09.png
---

I built a scene using OpenGL, combining basic meshes, texturing them, and adding lights.

## Details
---

I set up what is essentially just a still life to use as reference.

![Photo of an empty glass potion bottle, a cloth photo album, and a butterfly jigsaw puzzle box.](https://arkatu.com/arkatech/assets/img/opengl_04.png)

I was given source code, which contained all the necessary mesh data for the primitive shapes. I transformed those meshes to replicate the forms from the photo, but I realized that I couldn't render the photo album spine without sacrificing its curve. Referencing the code to render a full cylinder, I added code to render a half cylinder, only rendering half of the faces.

```c++
glDrawArrays(GL_TRIANGLE_STRIP, 72, 72);    // Half of sides
```

![Screenshot of various forms to resemble the bottle, album, and puzzle box. Each component is brightly colored for visiblity.](https://arkatu.com/arkatech/assets/img/opengl_05.png)

For most of the textures, I took photos of the objects and edited the images.

However, the original code had each face of a cube use the same texture. I added two new cube meshes that used texture atlases, one for the photo album and one for the top of the puzzle box. I edited the top textures and side textures together and caluclated the coordinates where each stopped and started.

![Texture atlas for the photo album, showing the front and back textures.](https://arkatu.com/arkatech/assets/img/opengl_12.jpg)

I intitally edited the puzzle box photos to be stacked vertically, but the textures didn't render. I realized that the images probably have dimensions that are a power of 2, so I edited the photos horizontally, which ended up working.

![Texture atlas for the puzzle box, showing the top and side textures.](https://arkatu.com/arkatech/assets/img/opengl_11.jpg)

I also needed to add a switch statement to allow other wrapping options.

```c++
// Edited to allow other texture wrapping options
switch (wrapping) {
    case mirrored_repeat:
        // mirrored repeat
        glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_MIRRORED_REPEAT);
        glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_MIRRORED_REPEAT);
        break;
    case clamp_to_edge:
        // clamp to edge
        glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
        glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
        break;
    case clamp_to_border:
        // clamp to border
        glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_BORDER);
        glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_BORDER);
        break;
    default:
        // repeat
        glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
        glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
        break;
}
```

## Results
---

![Screenshot of finished scene, showing the textured objects against a marble plane with lighting.](https://arkatu.com/arkatech/assets/img/opengl_09.png)

![Side view of finished scene with the glass of the bottle more visible.](https://arkatu.com/arkatech/assets/img/opengl_10.png)
