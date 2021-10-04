# Sehender Raum / Seeing Space (WIP)
## Notes about capturing, rendering and digitally reconstruction the world
When I learned about **traditional computer graphics and photogrammetry** I missed the big picture about how all the pieces connect: with hardware, physics and machine learning aspects. It made it harder to understand recent research and its meaning for the field. Rendering 3D models from 2D images remains a challenging problem but incredible progress has been made since I first became interested in the topic 20 years ago.

Catching up with newer research in image based rendering: A TLDR on how traditional computer graphics fits with computer vision, machine learning and capture hardware.

- [Data-Driven Computational Imaging](#data-driven-computational-imaging)
  * [Image-based rendering (IBR): Plenoptic function and capture](#image-based-rendering--ibr---plenoptic-function-and-capture)
    + [The Plenoptic function (Adelson and Bergen, 1991)](#the-plenoptic-function--adelson-and-bergen--1991-)
      - [The rendering equation (published in 1986) describes physical light transport for a single camera or the human vision](#the-rendering-equation--published-in-1986--describes-physical-light-transport-for-a-single-camera-or-the-human-vision)
      - [The plenoptic function is also inspired by multi-faceted **insect eyes or lens arrays**.](#the-plenoptic-function-is-also-inspired-by-multi-faceted---insect-eyes-or-lens-arrays--)
    + [5D and 4D Lightfields: capture and rendering (Andrey Gershun, 1936)](#5d-and-4d-lightfields--capture-and-rendering--andrey-gershun--1936-)
      - [Capturing, storing and compressing static and dynamic light fields](#capturing--storing-and-compressing-static-and-dynamic-light-fields)
        * [Multi-plane image (MPI) format and DeepMPI representation](#multi-plane-image--mpi--format-and-deepmpi-representation)
        * [Compression](#compression)
      - [Novel (virtual) 2D view synthesis form plenoptic samples](#novel--virtual--2d-view-synthesis-form-plenoptic-samples)
        * [3d scene reconstruction and inverse and differential rendering](#3d-scene-reconstruction-and-inverse-and-differential-rendering)
          + [Inverse rendering and differential rendering: explicitly reconstructing the scene](#inverse-rendering-and-differential-rendering--explicitly-reconstructing-the-scene)
        * [Neural Radiance Fields (NeRF): Representing Scenes as Neural Radiance Fields for View Synthesis (published 2020 Mildenhall et al.)](#neural-radiance-fields--nerf---representing-scenes-as-neural-radiance-fields-for-view-synthesis--published-2020-mildenhall-et-al-)
          + [Crowdsampling the Plenoptic Function with NeRF (published 2020)](#crowdsampling-the-plenoptic-function-with-nerf--published-2020-)
      - [Relighting with 4D Incident Light Fields](#relighting-with-4d-incident-light-fields)
        * [Relighting with NeRF](#relighting-with-nerf)
    + [Temporally Coded Imaging: Time Resolved Imaging (TRI) or Time-of-Flight (ToF) Imaging and LIDAR](#temporally-coded-imaging--time-resolved-imaging--tri--or-time-of-flight--tof--imaging-and-lidar)
  * [Related fields](#related-fields)
    + [Photogrammetry (first mentioned in 1867)](#photogrammetry--first-mentioned-in-1867-)
    + [Computional imaging for machines (not human observer)](#computional-imaging-for-machines--not-human-observer-)
      - [Lensless cameras (mid-1990s): ptical images for a computer—not for a human](#lensless-cameras--mid-1990s---ptical-images-for-a-computer-not-for-a-human)
      - [Simultaneous localization and mapping: SLAM (1986). Generating machine readable near realtime maps](#simultaneous-localization-and-mapping--slam--1986--generating-machine-readable-near-realtime-maps)
- [Important concepts](#important-concepts)
- [Recommended reading](#recommended-reading)
  * [Image-based rendering](#image-based-rendering)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# Data-Driven Computational Imaging
Computational imaging (CI) is a class of imaging systems that, starting from an imperfect physical measurement and prior
knowledge about the class of objects or scenes being imaged, deliver estimates of a specific object or scene presented to the imaging system.

## Image-based rendering (IBR): Plenoptic function and capture
In contrast to classical rendering, which projects 3D content to the 2D plane, image-based rendering techniques generate novel images by transforming an existing set of images, typically by warping and compositing them together.

A typical neural rendering approach takes as input images corresponding to certain scene conditions (for example, viewpoint, lighting, layout, etc.), builds a **"neural” scene representation** from them, and "renders” this representation under novel scene properties to synthesize novel images. 

The learned scene representation is not restricted by simple scene modeling approximations and can be optimized for high quality novel images. At the same time, neural rendering approaches incorporate ideas from classical graphics—in the form of input features, scene representations, and network architectures—to make the learning task easier, and the output more controllable. Neural rendering has many important use cases such as semantic photo manipulation, novel view synthesis, relighting, free viewpoint video, as well as facial and body reenactment.

Neural Rerendering combines classical 3D representation and renderer with deep neural networks that rerender the classical render into a more complete and realistic views.
In contrast to Neural Image-based Rendering (N-IBR), neural rerendering does not use input views at runtime, and instead relies on the deep neural network to recover the missing details.  Aartifacts such as ghosting, blur, holes, or seams can arise due to view-dependent effects, imperfect proxy geometry or too few source images. To address these issues, N-IBR methods replace the heuristics often found in classical IBR methods with learned blending functions or corrections that take into account view-dependent effects.


### The Plenoptic function (Adelson and Bergen, 1991)
The world as we see it using our eyes is a continuous three-dimensional function of the spatial coordinates. To generate photo-realistic views of a real-world scene from any viewpoint, it not only requires to understand the 3D scene geometry, but also to model complex viewpoint-dependent appearance resulting of light transport phenomena. A photograph is a two-dimensional map of the “no of photons” that map from the three-dimensional scene.

A point in the scene is imaged by measuring the **emitted and reflected light** that converges on the sensor plane. **Radiance (L)** represents the ray strength, measuring the combined angular and spatial power densities. Radiance can be used to indicate how much of the power emitted by the light source that is reflected, transmitted or absorbed by a surface will be captured by a camera facing that surface from a specified angle of view. 

#### The rendering equation (published in 1986) describes physical light transport for a single camera or the human vision
Classical computer graphics methods approximate the physical
process of image formation in the real world: light sources emit
photons that interact with the objects in the scene, as a function
of their geometry and material properties, before being recorded
by a camera. This process is known as light transport.

The process of transforming a scene definition including cameras,
lights, surface geometry and material into a simulated camera image
is known as rendering. The two most common approaches to
rendering are rasterization and raytracing: Rasterization is a feedforward
process in which geometry is transformed into the image
domain, sometimes in back-to-front order known as painter’s algorithm.
Raytracing is a process in which rays are cast backwards
from the image pixels into a virtual scene, and reflections and refractions
are simulated by recursively casting new rays from the intersections with the geometry

<img src="https://user-images.githubusercontent.com/74843139/134788604-b920d1c9-bb65-408a-9eb1-eab3ea1d4408.png" width=300><img src="https://user-images.githubusercontent.com/74843139/134789211-04ab96e8-04e5-4571-8437-8907bd98e58b.png" width=300>
 
<small><i>Source: Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>
 
 
 
While the rendering equation is a useful model for computer graphics some problems are easier to solve by a more generalized light model.
#### The plenoptic function is also inspired by multi-faceted **insect eyes or lens arrays**.

<img src="https://user-images.githubusercontent.com/74843139/135706284-64636f81-b20d-429d-ba5c-2d55c5c6df02.png" width=150><img src="https://user-images.githubusercontent.com/74843139/134789523-accc48f7-988b-472f-8fbb-2dc7524a295a.png" width=550>

<small><i>Source: https://en.wikipedia.org/wiki/Compound_eye, Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>

<img src="https://user-images.githubusercontent.com/74843139/135736699-8ecb616b-9550-435c-b552-9825c702e7ec.png" width=350><img src="https://user-images.githubusercontent.com/74843139/135750276-9117a3c0-1736-44c2-85d5-697a02a38cbc.png" width=250><img src="https://user-images.githubusercontent.com/74843139/135749914-1c2b8ff7-7e18-4146-ab1a-b2accc62c4c2.png" width=250>
<small><i>Source: Rendering for Data Driven Computational Imaging, Tristan Swedish, https://www.blitznotes.org/ib/physics/waves.html
https://courses.lumenlearning.com/boundless-chemistry/chapter/the-nature-of-light/</i></small>

The plenoptic function describes the **degrees of freedom of a light ray with the paramters**: Irradiance, position, wavelength, time, angle, phase, polarization, and bounce. 
Light has the properties of waves. 

Like ocean waves, light waves have crests and troughs.
* The distance between one crest and the next, which is the same as the distance between one trough and the next, is called the **wavelength**. 
* **Wave phase** is the offset of a wave from a given point. When two waves cross paths, they either cancel each other out or compliment each other, depending on their phase. 
* **Irradiance** is the amount of light energy from one thing hitting a square meter of another each second. Photons that carry this energy have wavelengths from energetic X-rays and gamma rays to visible light to the infrared and radio.
* The full equation is also **time dependent**.
* Polarization
    
### 5D and 4D Lightfields: capture and rendering (Andrey Gershun, 1936)
A **Light field** is a mathematical function of one or more variables whose range is a set of multidimensional vectors that describe the **amount of light flowing in every direction through every point in space***. It restricts the information to light outside the **convex hull** of the objects of interest. The magnitude of each ray is given by the **radiance** and the space of all possible light rays is given by the five-dimensional plenoptic function. The 7D plenoptic function can under certain assumptions and relaxations simplify o a **4D light field**, which is easier to sample and operate on.
A hologram is a photographic recording of a light field, rather than an image formed by a lens. 

The 4D lightfield has **2D spatial (x,y) and 2D angular (u,v)** information that is captured by a plenoptic sensor.
• the **incident light field** Li(u, v, alpha, beta) describing the **irradiance** of light incident on objects in space
• the **radiant light field** Lr (u, v, alpha, beta) quantifying the irradiance created by an object


#### Capturing, storing and compressing static and dynamic light fields
One type uses an array of micro-lenses placed in front of an otherwise conventional image sensor to sense intensity, color, and directional information. Multi-camera arrays are another type. Compared to a traditional photo camera that only captures the intensity of the incident light, a light-field camera provides **angular information** for each pixel. In principle, this additional information allows 2D images to be reconstructed at a given focal plane, and hence a depth map can be computed.
While **special cameras and cameras arrangements** have been build to capture light fields it is also possible them with a conventional camera or smart phone under certain constraints (see [Crowdsampling the Plenoptic Function](#-crowdsampling-the-plenoptic-function--a-name--crowdsampling--)).

<img src="https://user-images.githubusercontent.com/74843139/134798665-82995833-8751-4944-a3dd-0ea99a376216.png" width=200><img src="https://user-images.githubusercontent.com/74843139/134801063-d3812c40-7696-499b-b168-10b6e44f4a21.png" width=200><img src="https://user-images.githubusercontent.com/74843139/134805766-295077e7-81ef-405b-9ad7-38e8c583a55f.png" width=250><img src="https://user-images.githubusercontent.com/74843139/135706121-e3a5d78a-9dfa-4247-b504-1ba32b1d11ed.png" width=150>
    
<small><i>Stanford light field camera; Right: Adobe (large) lens array, source https://cs.brown.edu/courses/csci1290/labs/lab_lightfields, "Lytro Illum", a discontinued commercially available light field camera</i></small>
 
##### Multi-plane image (MPI) format and DeepMPI representation
Deep image or video generation approaches that enable explicit or implicit control of scene properties such as illumination, camera parameters, pose, geometry, appearance, and semantic structure.
MPIs have the ability to produce high-quality novel views of complex scenes in real time and the view consistency that arises from a 3D scene representation (in contrast to neural rendering approaches that decode a separate view for each desired viewpoint).

<img src="https://user-images.githubusercontent.com/74843139/135738631-e9a72fde-c4d4-46f4-8c1e-1823c6607090.png" width=200>
<img src="https://user-images.githubusercontent.com/74843139/135738753-1067733d-2d60-45a8-bf9d-3b2dfab83f41.png" width=400>

Stereo Magnification: Learning view synthesis using multiplane images

DeepMPI extends prior work on multiplane images (MPIs) to model viewing conditions that vary with time.

##### Compression
 
#### Novel (virtual) 2D view synthesis form plenoptic samples
Given a **dense sampling** of views, photorealistic novel views can be reconstructed by simple light field sample interpolation techniques. For novel view synthesis with **sparser view** sampling, the computer vision and graphics communities have made significant progress by predicting traditional geometry and appearance representations from observed images. The study of image-based rendering is motivated by a simple question: how do we use a finite set of images to reconstruct an infinite set of views.

View synthesis can be approached by either explicit estimation of scene geometry and color, or using coarser estimates of geometry to guide interpolation between captured views. 
One approach aims to **explicitly reconstruct the surface geometry** and the appearance on the surface from the observed sparse views, other approaches adopt volume-based representations to directly to model the appearance of the entire space and use volumetric rendering techniques to generate images for 2D displays. 
The raw samples of a light field are saved as disks. resolution large amounts of data

<img src="https://user-images.githubusercontent.com/74843139/135805897-45ba61f4-2c3f-47bf-865f-8a1b52d792cf.png" width=400>

State of the Art on Neural Rendering

<img src="https://user-images.githubusercontent.com/74843139/134803822-801b99d7-5bd7-4ab3-8f12-0eba63eedcd5.png" width=300><img src="https://user-images.githubusercontent.com/74843139/135702022-93723d7a-fb76-4380-8150-7aaceff96757.png" width=300>

<small><i>Source: https://github.com/Arne-Petersen/Plenoptic-Simulation, A System for Acquiring, Processing, and Rendering Panoramic Light Field Stills for Virtual Reality</i></small>
 
Light field rendering pushes the latter strategy to an extreme by using dense structured sampling of the lightfield to make re-construction guarantees independent of specific scene geometry. Most image based renering algorithms are designed to model static appearance, DeepMPI (Deep Multiplane Images), which further captures viewing condition dependent appearance. 

**Camera calibration** is often assumed to be prerequisite, while in practise, this information is rarely accessible, and requires to be pre-computed with conventional techniques, such as SfM.
 
##### 3d scene reconstruction and inverse and differential rendering 
###### Inverse rendering and differential rendering: explicitly reconstructing the scene
They can be classified into
explicit and implicit representations. Explicit methods describe
scenes as a collection of geometric primitives, such as triangles,
point-like primitives, or higher-order parametric surfaces.
One popular class of approaches uses mesh-based representations of scenes with either use [48] or view-dependent [2,8,49] appearance. Differentiable rasterizers [4,10,23,25] or pathtracers [22,30] can directly optimize mesh representations to reproduce a set of input images using gradient descent.
However, gradient-based mesh optimization based on image reprojection is often
dicult, likely because of local minima or poor conditioning of the loss land-
scape. Furthermore, this strategy requires a template mesh with xed topology
to be provided as an initialization before optimization [22], which is typically
unavailable for unconstrained real-world scenes.
Differentiable Rendering promises to close the loop between computer Vision and Graphics.

Inverse rendering aims to estimate physical attributes of a scene, e.g., reflectance, geometry, and lighting, from image(s).
Also called **Differentiable Rendering** it promises to close the loop between computer vision and graphics.

##### Neural Radiance Fields (NeRF): Representing Scenes as Neural Radiance Fields for View Synthesis (published 2020 Mildenhall et al.) 
A recent and popular **volumetric rendering technique** to generate images is Neural Radiance Fields (NeRF) due to its exceptional simplicity and performance for synthesising high-quality images of complex real-world scenes. 
The key idea in NeRF is to represent the entire volume space with a continuous function, parameterised by a **multi-layer perceptron (MLP)**, bypassing the need to discretise the space into voxel grids, which usually suffers from resolution constraints.
It allows real-time synthesis of photorealistic new views.

<img src="https://user-images.githubusercontent.com/74843139/135739158-186170f4-ad7f-4734-aceb-24a7db7c02dd.png" width=300> <img src="https://user-images.githubusercontent.com/74843139/135739188-2b9ac37a-3105-4d7e-944c-9549e40d3c6c.png" width=300>

https://dellaert.github.io/NeRF/

Neural volume rendering refers to methods that generate images or video by tracing a ray into the scene and taking an integral of some sort over the length of the ray. Typically a neural network like a multi-layer perceptron encodes a function from the 3D coordinates on the ray to quantities like **density and color**, which are integrated to yield an image. One of the reasons NeRF is able to render with great detail is because it encodes a 3D point and associated view direction on a ray using periodic activation functions, i.e., Fourier Features. 
the impact of the NeRF paper lies in its brutal simplicity: just an MLP taking in a 5D coordinate and outputting density and color. There are some bells and whistles, notably the positional encoding and a stratified sampling scheme, but many researchers were taken aback (I think) that such a simple architecture could yield such impressive results. That being said, vanilla NeRF left many opportunities to improve upon speed for training and rendering.    It can only represent static scenes
    It “bakes in” lighting.    A trained NeRF representation does not generalize to other scenes/objects.

https://user-images.githubusercontent.com/74843139/135747420-4d91bc80-2893-44a4-8d32-16bf7024b4f2.mp4

https://dellaert.github.io/NeRF/
 
 A deeper integration of graphics knowledge into the network is possible based
on differentiable graphics modules. Such a differentiable module
can for example implement a complete computer graphics renderer, a 3D rotation, or an illumination model. Such components add a physically
inspired inductive bias to the network, while still allowing
for end-to-end training via backpropagation. This can be used to
analytically enforce a truth about the world in the network structure,
frees up network capacity, and leads to better generalization,
especially if only limited training data is available.

###### Crowdsampling the Plenoptic Function with NeRF (published 2020)
Given a large number of **tourist photos taken at different times of day**, this machine learning based approach learns to construct a continuous set of light fields and to synthesize novel views capturing all-times-of-day scene appearance. achieve convincing changes across a variety of times of day and lighting conditions.
mask out transient objects such as people and cars during training and evaluation

<img src="https://user-images.githubusercontent.com/74843139/134799704-32fd77d0-f71e-47aa-b763-fcef4af17e9d.png" width=500>
![image](https://research.cs.cornell.edu/crowdplenoptic/teaser/coeur_teaser.gif)![image](https://research.cs.cornell.edu/crowdplenoptic/teaser/trevi_teaser.gif)![image](https://research.cs.cornell.edu/crowdplenoptic/teaser/rock_teaser.gif)![image]( https://research.cs.cornell.edu/crowdplenoptic/teaser/pantheon_teaser.gif)
<small><i>Source: https://www.semanticscholar.org/paper/Crowdsampling-the-Plenoptic-Function-Li-Xian</i></small>
 

#### Relighting with 4D Incident Light Fields
It is possible to re-light and de-light real objects illuminated by a 4D incident light field, representing the illumination of an environment. By exploiting the richness in angular and spatial variation of the light field, objects can be relit with a high degree of realism.


<img src="https://user-images.githubusercontent.com/74843139/135739588-00789dba-9ddc-45a8-bc44-5a9f5c0fc7da.png" width=500>
https://en.wikipedia.org/wiki/Light_stage
 
##### Relighting with NeRF
Another dimension in which NeRF-style methods have been augmented is in how to deal with lighting, typically through latent codes that can be used to re-light a scene. 
NeRF-W was one of the first follow-up works on NeRF, and optimizes a latent appearance code to enable learning a neural scene representation from less controlled multi-view collections.

Neural Reflectance Fields improve on NeRF by adding a local reflection model in addition to density. It yields impressive relighting results, albeit from single point light sources. NeRV uses a second “visibility” MLP to support arbitrary environment lighting and “one-bounce” indirect illumination.

<img src="https://user-images.githubusercontent.com/74843139/135751323-ef8582a0-575d-41fb-9a40-861fbbbd35d3.png" width=500>
nerf in the wild


### Temporally Coded Imaging: Time Resolved Imaging (TRI) or Time-of-Flight (ToF) Imaging and LIDAR
ToF refers to the use of the **speed of light or even sound** to determine distance, as it measures the time it takes light to leave a device, bounce off an object or plane, and return to the device, all divided by two reveals The distance from the device to the object or plane.
ToF applications create "depth maps" based on light detection, usually with a standard RGB camera, and the advantage that ToF offers compared to LiDAR is that ToF requires less specialized equipment so that it can be used with smaller, cheaper devices.

<img src="https://user-images.githubusercontent.com/74843139/134803482-e283e016-a50e-4ae9-aca7-d35128ba9554.png" width=500>
<small><i>Source: Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>
    
<img src="(https://user-images.githubusercontent.com/74843139/134806495-5d40deff-328c-42b5-ada4-2035399abf1f.png" width=200>
<small><i>Source: CVPR 2019 Data-Driven Computational Imaging</i></small>
 
     
## Related fields
### Photogrammetry (first mentioned in 1867)
Photogrammetry is the science of **reconstructing objects and environments that exist in the physical world** through photographs. The technique involves stitching together large collections of overlapping photographs to create **topographical maps, point clouds** and may also produce **2D and 3D digital models**. First mentioned in 1867 it predates digital photography significantly.

Photogrammetry data is usually captured with a **single moving conventional still frame camera** or uses **aerial data**. Sometimes it is combined with LIDAR data for depth information. Visibility constraints such as rain, occlusion or dense vegetation can block the camera's line of sight or limit light required for good result
 
<img src="https://user-images.githubusercontent.com/74843139/134804512-4c7ab394-319e-4952-895c-405799bf5073.png" width=300>
<small><i>Source: http://www.aamspi.com/services/aerial-photogrammetry/</i></small>

### Computional imaging for machines (not human observer)
#### Lensless cameras (mid-1990s): Optical images for a computer (not for a human oberserver)
The basic design of a camera has remained unchanged for centuries. To acquire an image, light from the scene under view is focused onto a photosensitive surface using a lens. The primary task of a lens in a camera is to shape the incoming light wavefront so that it creates a focused
Lenses introduce a number of limitations, specifically all traditional cameras have limited depth of field. Cameras also end up being thick due to the lens complexity and the large distance required between the lens and sensor to achieve focus.
Lensless imaging systems dispense with a lens by using other optical elements to manipulate the incoming light. The sensor records the intensity of the manipulated light, which may not appear as a focused image. However, when the system is designed correctly, the image can be recovered
from the sensor measurements with the help of a computational algorithm.
The simplest but inefficient lensless imaging system is the pinhole camera, coded aperture cameras improve the light efficiency using a mask with an array of
pinholes.

<img src="https://user-images.githubusercontent.com/74843139/135749276-a0078caa-c376-45e2-a75b-a991629991f2.png" width=500>
 https://zeenews.india.com/india/iit-m-us-researchers-develop-algorithms-to-get-clearer-images-from-futuristic-lensless-cameras-2362619.html



####  Simultaneous localization and mapping: SLAM (1986). Generating machine readable near realtime maps
Achieving real-time perception is critical to developing a fully autonomous system that can sense, navigate, and interact with its environment. Perception tasks such as online 3D reconstruction and mapping .
 ![image](https://user-images.githubusercontent.com/74843139/135749450-762580f6-30d8-478c-b45c-0372756dba53.png" width=500>
 https://medium.com/@hurmh92/autonomous-driving-slam-and-3d-mapping-robot-e3cca3c52e95
 

# Important concepts
 * *DeepMPI*<a name="deepmpi"> Deep Multiplane Images
 * *Light field* is a mathematical function of one or more variables whose range is a set of multidimensional vectors that describe the **amount of light flowing in every direction through every point in space***
 * *Radiance*<a name="radiance"> represents the ray strength, measuring the combined angular and spatial power densities. Radiance can be used to indicate how much of the power emitted by the light source that is reflected, transmitted or absorbed by a surface will be captured by a camera facing that surface from a specified angle of view.
 * The irradiance E of a surface is defined as the incident radiant flux density, and has units of W· m−2. Meanwhile, radiance L of a surface is emitted flux per unit of foreshortened area (from Lambert’s Law) per unit solid angle W· m−2 · sr−1.
    

# Recommended reading

## Image-based rendering
* *Computational Imaging* By Ayush Bhandari, Achuta Kadambi and Ramesh Raskar, A comprehensive and up-to-date textbook and reference for computational imaging, which combines vision, graphics, signal processing, and optics, http://compimagingbook.com
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling the Plenoptic Function
* Data-Driven Computational Imaging http://ciml.media.mit.edu, CVPR 2019
* https://cseweb.ucsd.edu/~viscomp/projects/LF/
* EUROGRAPHICS 2006 Tutorial "Inverse Rendering: From Concept to Applications", https://diglib.eg.org/bitstream/handle/10.2312/egt.20061062.0399-0547/0399-0547.pdf
* *CVPR 2021 Tutorial on Physics-Based Differentiable Rendering*, June 20, 2021,  https://diff-render.org
* https://raytrix.de Realtime Plenoptic Metrology
* https://github.com/Arne-Petersen/Plenoptic-Simulation
* https://en.wikipedia.org/wiki/Light_field 

 
 ## Visual Sensing Using Machine Learning
* https://www.americanscientist.org/article/imaging-without-lenses
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling Plenoptic Function
* https://github.com/tensorflow/graphics
* https://arxiv.org/abs/2102.07064, 2021 NeRF--: Neural Radiance Fields Without Known Camera Parameters Zirui Wang, Shangzhe Wu, Weidi Xie, Min Chen, Victor Adrian Prisacariu
* https://www.techeblog.com/google-nerf-in-the-wild-2d-photo-3d-model/
* https://dellaert.github.io/NeRF,Frank Dellaert, NeRF Explosion 2020
 * https://www.pauldebevec.com reasearch and their github https://github.com/augmentedperception
 *EUROGRAPHICS 2020, State of the Art on Neural Rendering
 *https://www.matthewtancik.com/nerf
