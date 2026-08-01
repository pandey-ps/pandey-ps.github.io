---
layout: post
title: "Ray Tracer in 99 Lines of Python"
date: 2026-02-28
---

You can find the full code here: [https://github.com/pandey-ps/raytracer](https://github.com/pandey-ps/raytracer)

I started this project as to learn the math behind the working of ray tracers as a weekend challenge. So, what I implemented is a CPU ray tracer that can render 3D models using triangle intersection and shading. As it is only of 99 lines in code and I just wanted to build a basic one for my learning, it is not optimized for acceleration but does the job. it's just NumPy, math and rays!

Used the `trimesh` library loader to load from `.obj` and related file types, further then we shoot rays from a virtual camera, compute the ray triangle intersection, add shading and output a PNG image (used Pillow to convert array to png).

The trade-off is speed, rendering [Suzanne (968 triangles)](https://github.com/alecjacobson/common-3d-test-models/blob/master/data/suzanne.obj) takes ~75 seconds on CPU. A BVH will speed this up, but that's another ~80 lines, I will add these going forward. You can see the output image at the repository readme.

Use Suzanne (blender monkey head), the second argument is the scale factor:

```shell
git clone https://github.com/pandey-ps/raytracer.git
cd raytracer && uv sync
curl -o suzanne.obj \
  https://raw.githubusercontent.com/alecjacobson/common-3d-test-models/master/data/suzanne.obj
python main.py suzanne.obj 1.0
```

**Ray Equation**

$$\mathbf{r}(t) = \mathbf{o} + t{d}$$

*o* is the origin; *d* is the normalized direction and *t>0*. We search for the smallest positive *t* where the ray intersects geometry.

**Ray Triangle Intersection (Möller Trumbore Algorithm)**

resource: [Möller-Trumbore Ray-Triangle Intersection (Scratchapixel)](https://scratchapixel.com/lessons/3d-basic-rendering/ray-tracing-rendering-a-triangle/moller-trumbore-ray-triangle-intersection.html)

tldr: given a triangle defined by its three vertices, we calculate two edge vectors and use cross products to find the determinant. From this, we solve for the barycentric coordinates *(u, v)* and the intersection distance *(t)*, which also gives us the surface normal (cross product). Together, *t* tells which triangle is closest to the camera and is visible, and the normal tells how the surface is shaded (surfaces facing the light appear bright, while surfaces facing away appear dark.)

**Lambertian Diffuse Shading**

resource: [Lambertian Reflectance (Wikipedia)](https://en.wikipedia.org/wiki/Lambertian_reflectance)

tldr: the brightness of a surface depends on the angle between the surface normal and the light direction. If the surface faces the light directly, it appears bright. If it faces away, it appears dark. This is calculated as the dot product of the normal and light direction, clamped to zero (no negative light). An ambient term is added so that surfaces in shadow aren't completely black, for my ray tracer, the darkest a surface can get is 20% brightness.

**Sky Gradient (Linear Interpolation)**

resource: [Ray Tracing in One Weekend](https://raytracing.github.io/books/RayTracingInOneWeekend.html)

tldr: for rays that don't hit any geometry, we blend between white and blue based on the ray's vertical direction. Rays pointing up get blue, rays pointing down get white, and everything in between gets a gradient. This is linear interpolation (lerp) using in blending like these.