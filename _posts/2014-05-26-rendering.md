---
layout: post
title: Rendering
---

In my second post I want to talk about rendering in [OpenGL](http://en.wikipedia.org/wiki/OpenGL).
(please beware i'm not an expert)

In modern games you have 16 to 33 milliseconds to render a frame (60/30 fps).
In this short time frame the CPU sends buffered commands to the GPU. 
The GPU than executes these commands on a list of GPU-side data buffers (vertex data, texture data, ...).

##Pipeline
In older GPU hardware most of the pipeline (data buffers -> pixels) was [fixed function](http://en.wikipedia.org/wiki/Fixed-function).
You could only give the GPU some vertex and texture data which then got rendered into a frame (called the framebuffer).
Modern hardware now allows us to control some of the pipeline stages with the help of a c-like language called [shaders](http://en.wikipedia.org/wiki/Shader).

##Shaders

