# Planet Maker

![Build Status](https://img.shields.io/github/last-commit/RaymondBello/Procedural-Geometry-Planet-Maker) ![Build Status](https://img.shields.io/github/issues-raw/RaymondBello/Procedural-Geometry-Planet-Maker) ![Build Status](https://img.shields.io/github/contributors/RaymondBello/Procedural-Geometry-Planet-Maker?color) 
![Build Status](https://img.shields.io/github/languages/top/RaymondBello/Procedural-Geometry-Planet-Maker) ![Build Status](https://img.shields.io/github/languages/count/RaymondBello/Procedural-Geometry-Planet-Maker) 
![Build Status](https://img.shields.io/github/repo-size/RaymondBello/Procedural-Geometry-Planet-Maker?color=red) 

Planet Maker is a simple unity-based tool, aimed at using Procedural Geometry to create a variety of 3D planets which can be exported as individual assets for 3D-printing or for use within any other graphic rendering game engine (such as Blender, Unity and UnrealEngine)

![gradient1.gif](https://www.dropbox.com/s/loi0errzjlj3was/gradient1.gif?dl=0&raw=1)

## Table of contents
* [Usage](#usage-steps)
* [New Features](#new-features)
* [Installation](#installation)
* [Implementation](#implementation)
* [Articles](#articles)
* [To-do](#to-dos)

# Usage 
  - Clone repository
  - Load Planet-Maker project into Unity (Unity 2019.4.4f1 used for development)
  - Select the 'Planet' Object in the Hierarchy Tab on the left.
  - Toggle Planet Settings Sliders in the Inspector Tab on the right to modify the planets appearance.
  - To Export modified planet: **Asset>Export Package**
  - Magic

### New Features!

  - Added Simplex Noise based on a paper by [Stefan Gustavson][simplex].
  - Added Multi-Noise Filter Layering for a more realistic terrain appearance on the planet surface.


# Installation

Planet-Maker requires [Unity on Windows](https://download.unity3d.com/download_unity/1f1dac67805b/UnityDownloadAssistant-2019.4.4f1.exe) or [Unity on Mac](https://download.unity3d.com/download_unity/1f1dac67805b/UnityDownloadAssistant-2019.4.4f1.dmg)  LTS Release Version 2019.4.4f1 and above to run.

Install a LTS Release of Unity 2019 for your target platform.
Navigate to a clean working directory then type the following in the terminal/cmd:

```sh
$ git clone https://github.com/RaymondBello/Procedural-Geometry-Planet-Maker.git
```


# Implementation
#### 1. Geometric Shape Generation
##### 1.1. Create a rectangular plane
Since we’re creating a rectangular plane, we only need 4 vertices, one for each corner. `Assets/TerrainFace.cs`
To create a cube, construct a mesh of all 6 plane faces of the cube from a local point.

##### 1.2. Add Triangles to the plane
To scale resolution, triangles are added to each plane. The resolution is described by the number triangle vertices along the edge of the plane.

![resolution1.gif](https://www.dropbox.com/s/g1podg6yvg3covq/resolution1.gif?dl=0&raw=1)

```
resolution = number of triangle vertices on the edge of the plane
total number of triangle vertices = resolution * resolution
total number of triangles on the plane face = (resolution - 1) * 2 * 3
```

##### 1.3 Normalize Unit Cube to Sphere
Construct a mesh of all 6 planes on the cube. The normalized points on the Unit Cube are assigned to the points on the Unit Sphere.
Resulting in elevation of the terrain at any point on the Unit Sphere.

![sphere1.gif](https://www.dropbox.com/s/gusj5kwmapotakq/sphere1.gif?dl=0&raw=1)

```
pointOnUnitCube = localUp + (percent.x - .5f) * 2 * axisA + (percent.y - .5f) * 2 * axisB;
pointOnUnitSphere = pointOnUnitCube.normalized;
unscaledElevation = shapeGenerator.CalculateUnscaledElevation(pointOnUnitSphere);
```

#### 2. Terrain
##### 2.1. Add Simplex Noise to elevation
To generate coherent noise, an open-source implementation of the [simplex noise algorithm][simplex] is added to the elevation .`Assets/Noise.cs`
Multiple layers of Noise can be added to each other to generate a more interesting terrain (Octaves).

![perlin-noise2.gif](https://www.dropbox.com/s/cy9okazeibsw4iy/perlin-noise2.gif?dl=0&raw=1)

##### 2.2. Add gradient colour shader for terrain and ocean depth
Using a MinMax function the `elevation` relative to the Unit Sphere Surface `Radius` can be determined.
The scaled elevation is evaluated as a number between `[0,1]`
So the Elevation at any point on the plabet can be described as:

```
elevation = Radius * (1 + elevation)
elevationMinMax.AddValue(elevation)
PointOnPlanet = pointOnUnitSphere * elevation
```

The colour gradient is then mapped to the terrain face using a UV mesh. 
Ocean Surface Smoothness can be toggled in the Material settings.

![gradient1.gif](https://www.dropbox.com/s/loi0errzjlj3was/gradient1.gif?dl=0&raw=1)

Thats it, you've made your own planet.

## Articles

This Planet Maker tool implements a couple of open source techniques/articles to aid in its functionality:

* [Intro to Procedural Geometry: Linden Reid][intro-p] - Bare basics of creating procedural geometry in Unity
* [Simplex Noise Demystified: Stefan Gustavson][simplex] - Noise algorithm that outputs 3-dimensional Simplex Perlin noise.
* [Unity's Gradient Module Documentation][gradient] - Gradient used for adding Elevation Colors and Ocean Depth colours.

And of course Planet-Maker itself is open source with a [public repository][planet] on GitHub.


### To-dos

 - Improve Render Time for higher mesh resolutions
 - Add an optimized Elevation Gradient scaling funtion that works for all Elevation points on various noise filters on Unit Sphere

License
----
MIT


**Author: Raymond Bello [2020]**

[//]: # 

   [simplex]: <http://staffwww.itn.liu.se/~stegu/simplexnoise/simplexnoise.pdf>
   [intro-p]: <https://lindenreid.wordpress.com/2018/01/20/intro-to-procedural-geometry-part-1/>
   [gradient]: <https://docs.unity3d.com/ScriptReference/Gradient.html>
   [planet]: <https://github.com/RaymondBello/Procedural-Geometry-Planet-Maker>
   [git-repo-url]: <https://github.com/RaymondBello/Procedural-Geometry-Planet-Maker.git>
  