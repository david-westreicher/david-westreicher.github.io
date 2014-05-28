---
layout: post
title: Radiosity Baker
---
{% include mathjs.html %}

In my second post I want to talk about a small project i implemented in my [fungine](https://github.com/david-westreicher/fungine/blob/master/src/rendering/GiRenderer.java).
Modern computer games achieve a high level of realism.
One of the most important part of this realism is the lighting.
In this example we see a textureless scene with 2 different lighting techniques:
{% include imagecaption.html url="/static/2014-05-26-globalillumination/Radiosity_Comparison.jpg" description="Image taken from [here](http://en.wikipedia.org/wiki/Radiosity_(computer_graphics))" %}

## Global Illumination (GI)
Normally a scene is rendered with [rasterization](http://en.wikipedia.org/wiki/Rasterisation) by projecting vertices onto an image plane.
With rasterizing you could render the image to the left by using shadow mapping (an approximation to real shadowing).
GI on the other hand tries to achieve the image to the right by taking a more physically plausible approach:
In nature a light source emits photons, which travel with the speed of light (in a vacuum) until they hit a surface:
{% include imagecaption.html url="/static/2014-05-26-globalillumination/diffuse_specular.png" description="Image taken from [Background: Physics and Math of Shading](http://blog.selfshadow.com/publications/s2013-shading-course/)" %}
By hitting a surface the energy of the photon is split into several parts:

* Diffuse: Think of the color of the material (some frequencies/colors of the light get absorbed by the surface). This part is view-invariant. 
No matter from which direction I look onto the surface, the color doesn't change.
* Specular: Think of a mirror (the entry angle is equal to the exit angle of the light source). The important thing is that this part is view-dependent.
* Heat: Some energy is absorbed by the material and converted into heat (we don't care about this part)

The diffuse and reflection part themselves act as new light sources which bounce around the scene hitting other surfaces.
These complex interactions create the image on the right.

## Radiosity
Radiosity is a subset of GI in the sense that it only takes diffuse-propagation into account.
In the example above we can see that the red floor reflects some red light to the white walls, which makes the image more realistic.
Because radiosity only works on the diffuse part its also view-invariant, 
which means we can precalculate the radiosity of the whole scene offline and store it in a so called [texture atlas](http://en.wikipedia.org/wiki/Texture_atlas)
(one texture for the whole scene).
This allows us to have more computation time online for other rendering effects (post-processing effects, gpu particles, ..., you name it).
This process is called "[lightmapping](http://en.wikipedia.org/wiki/Lightmap)" or "lightmap baking" and is an old trick popularized by the game Quake.

## Algorithm
To implement this effect I followed [this](http://freespace.virgin.net/hugo.elias/radiosity/radiosity.htm) great but dated article.
The basic idea is that you render the scene from every [texel](http://en.wikipedia.org/wiki/Texel_(graphics)) in the scene and update its color.

1. start with a triangle soup (a large number of triangles)
2. create a texture atlas by fitting all triangles sequentially into the texture. Every pixel \\(p_i = (c_i,n_i,pos_i)\\) contains 9 values
	* 3 for the color \\(c_i\\)
	* 3 for the normal \\(n_i\\)
	* 3 for the [interpolated position](http://en.wikipedia.org/wiki/Barycentric_coordinate_system) \\(pos_i\\)
3. upload a black texture (the same size as the atlas) with pixels \\(gpu_i\\) to the GPU
4. until convergence of all \\(gpu_i\\)
	1. for every pixel \\(p_i\\) in the texture atlas
	2. put the camera to the position \\(pos_i\\) facing the normal \\(n_i\\)
	3. render the scene with the GPU
	4. download the rendered image to the CPU
	5. \\(avg:=\\) the average of the rendered image in color space
	6. \\(gpu_i:=c_i*avg*R\\) , where \\(R\\) is the diffuse reflectivity and depends on the material

## Implementation

## Results

## Possible Improvements

