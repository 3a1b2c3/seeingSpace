# Sehender Raum / Seeing Space (WIP)
## Notes about capturing, rendering and digitally reconstruction the world
When I learned about **traditional computer graphics and photogrammetry** I missed the big picture about how all the pieces connect: with hardware, physics and machine learning aspects. It made it harder to understand recent research and its meaning for the field. Rendering 3D models from 2D images remains a challenging problem but incredible progress has been made since I first became interested in the topic 20 years ago (see below).

Catching up with newer research in image based rendering: A TLDR on how traditional computer graphics fits with computer vision, machine learning and capture hardware.

- [Classic rendering, computer graphics](#classic-rendering--computer-graphics)
  * [The rendering equation<a name="requation"> (published in 1986)](#the-rendering-equation-a-name--requation----published-in-1986-)
- [Neural Rerendering](#neural-rerendering)
- [Image-based rendering (IBR): Plenoptic function and capture](#image-based-rendering--ibr---plenoptic-function-and-capture)
  * [The Plenoptic function (Adelson and Bergen, 1991)](#the-plenoptic-function--adelson-and-bergen--1991-)
    + [The plenoptic function is also inspired by multi-faceted **insect eyes or lens arrays**.](#the-plenoptic-function-is-also-inspired-by-multi-faceted---insect-eyes-or-lens-arrays--)
  * [5D and 4D Lightfields: capture and rendering (Andrey Gershun, 1936)](#5d-and-4d-lightfields--capture-and-rendering--andrey-gershun--1936-)
    + [Capturing, storing and compressing static and dynamic light fields](#capturing--storing-and-compressing-static-and-dynamic-light-fields)
      - [Neural Scene representations](#neural-scene-representations)
        * [Networks, Acorn: Adaptive coordinate networks for neural scene representation (2021)](#networks--acorn--adaptive-coordinate-networks-for-neural-scene-representation--2021-)
        * [Multi-plane image (MPI) format and DeepMPI representation (2.5 D)](#multi-plane-image--mpi--format-and-deepmpi-representation--25-d-)
      - [Compression](#compression)
    + [Novel (virtual) 2D view synthesis from plenoptic samples](#novel--virtual--2d-view-synthesis-from-plenoptic-samples)
      - [3d scene reconstruction and inverse and differential rendering](#3d-scene-reconstruction-and-inverse-and-differential-rendering)
        * [Inverse rendering and differential rendering: explicitly reconstructing the scene](#inverse-rendering-and-differential-rendering--explicitly-reconstructing-the-scene)
      - [Novel view synthesis with neural rendering: Volume Rendering with Radiance Fields](#novel-view-synthesis-with-neural-rendering--volume-rendering-with-radiance-fields)
        * [Neural Radiance Fields (NeRF): Representing Scenes as Neural Radiance Fields for View Synthesis (published 2020 Mildenhall et al.)](#neural-radiance-fields--nerf---representing-scenes-as-neural-radiance-fields-for-view-synthesis--published-2020-mildenhall-et-al-)
          + [Crowdsampling the Plenoptic Function with NeRF (published 2020)](#crowdsampling-the-plenoptic-function-with-nerf--published-2020-)
      - [Towards Instant 3D Capture (with a cell phone): Nerfies](#towards-instant-3d-capture--with-a-cell-phone---nerfies)
    + [Relighting with 4D Incident Light Fields](#relighting-with-4d-incident-light-fields)
      - [Relighting with NeRF](#relighting-with-nerf)
        * [NeRD: Neural Reflectance Decomposition from Image Collections](#nerd--neural-reflectance-decomposition-from-image-collections)
      - [Editable NeRFs](#editable-nerfs)
  * [Temporally Coded Imaging: Time Resolved Imaging (TRI) or Time-of-Flight (ToF) Imaging and LIDAR (1961)](#temporally-coded-imaging--time-resolved-imaging--tri--or-time-of-flight--tof--imaging-and-lidar--1961-)
- [Related fields](#related-fields)
  * [Photogrammetry (first mentioned in 1867)](#photogrammetry--first-mentioned-in-1867-)
  * [Computional imaging for machines (not human observer)](#computional-imaging-for-machines--not-human-observer-)
    + [Lensless cameras (mid-1990s): Optical images for a computer (not for a human oberserver)](#lensless-cameras--mid-1990s---optical-images-for-a-computer--not-for-a-human-oberserver-)
    + [Simultaneous localization and mapping: SLAM (1986). Generating machine readable near realtime maps](#simultaneous-localization-and-mapping--slam--1986--generating-machine-readable-near-realtime-maps)
- [Conclusion](#conclusion)
- [Important concepts](#important-concepts)
- [Recommended reading](#recommended-reading)
  * [Neural Rendering and Nerf explosion](#neural-rendering-and-nerf-explosion)
  * [Other](#other)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# Classic rendering, computer graphics
Classical computer graphics methods approximate the physical process of image formation in the real world: light sources emit photons that interact with the objects in the scene, as a function of their geometry and material properties, before being recorded by a camera. This process is known as **light transport**.
The process of transforming a scene definition including cameras, lights, surface geometry and material into a simulated camera image is known as **rendering**.

The two most common approaches to rendering are rasterization and raytracing.
-**Rasterization** is a feedforward process in which geometry is transformed into the image domain, sometimes in back-to-front order known as painter’s algorithm. 
-**Raytracing** is a process in which rays are cast backwards from the image pixels into a virtual scene, and reflections and refractions are simulated by recursively casting new **rays from the intersections** with the geometry.

<img src="https://user-images.githubusercontent.com/74843139/134789211-04ab96e8-04e5-4571-8437-8907bd98e58b.png" width=400>
 
## The rendering equation<a name="requation"> (published in 1986) 

The rendering equation describes **physical light transport** for a **single camera or the human vision**.
A point in the scene is imaged by measuring the **emitted and reflected light** that converges on the sensor plane. **Radiance (L)** represents the ray strength, measuring the combined angular and spatial power densities. Radiance can be used to indicate how much of the power emitted by the light source that is reflected, transmitted or absorbed by a surface will be captured by a camera facing that surface from a specified angle of view.

<img src="https://user-images.githubusercontent.com/74843139/135806961-3278e761-d91a-4fc7-b3bd-a58b68123fff.png" width=400><img src="https://user-images.githubusercontent.com/74843139/134788604-b920d1c9-bb65-408a-9eb1-eab3ea1d4408.png" width=400>
 
<small><i>Source:https://www.mdpi.com/2072-4292/13/13/2640, Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>
 
 # Neural Rerendering
 
Neural Rerendering is a relative new technique that combines classical or other 3D representation and renderer with deep neural networks that rerender the classical render into a more complete and realistic views. In contrast to **Neural Image-based Rendering (N-IBR)**, neural rerendering does not use input views at runtime, and instead relies on the deep neural network to recover the missing details.
A typical neural rendering approach takes as input images corresponding to certain scene conditions (for example, viewpoint, lighting, layout, etc.), builds a "neural” scene representation from them, and "renders” this representation under novel scene properties to synthesize novel images.
 
The learned scene representation is not restricted by simple scene modeling approximations and can be optimized for high quality novel images. At the same time, neural rendering approaches incorporate ideas from classical graphics—in the form of input features, scene representations, and network architectures—to make the learning task easier, and the output more controllable. Neural rendering has many important use cases such as semantic photo manipulation, novel view synthesis, relighting, free viewpoint video, as well as facial and body reenactment.
 
<img src="https://user-images.githubusercontent.com/74843139/137432875-74ca25c1-dcf1-4126-ac03-ad5c5c703fce.png" width=450> <img src="https://user-images.githubusercontent.com/74843139/137667708-cb8541fa-5758-476a-bf5e-e4548ad5a858.png" width=550>

  
<small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
 <small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
 
Artifacts such as ghosting, blur, holes, or seams can arise due to view-dependent effects, imperfect proxy geometry or too few source images. To address these issues, N-IBR methods replace the heuristics often found in classical IBR methods with learned blending functions or corrections that take into account view-dependent effects.
 
<img src="https://user-images.githubusercontent.com/74843139/137408495-4ab043d4-ddd8-420d-9d12-f768b6336b5c.png" width=450>
 
<small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
 
# Image-based rendering (IBR): Plenoptic function and capture
Computational imaging (CI) is a class of imaging systems that, starting from an imperfect physical measurement and prior
knowledge about the class of objects or scenes being imaged, deliver estimates of a specific object or scene presented to the imaging system.

In contrast to classical rendering, which projects 3D content to the 2D plane, image-based rendering techniques generate novel images by transforming an existing set of images, typically by warping and compositing them together. The essence of image-based rendering technology is to obtain all the visual information of the scene directly through images.
Its used in computer graphics and computer vision, and it is also widely used in virtual reality technology. 


## The Plenoptic function (Adelson and Bergen, 1991)
The world as we see it using our eyes is a continuous three-dimensional function of the spatial coordinates. To generate photo-realistic views of a real-world scene from any viewpoint, it not only requires to understand the 3D scene geometry, but also to model complex **viewpoint-dependent appearance resulting of light transport phenomena**. A photograph is a two-dimensional map of the “number of photons” that map from the three-dimensional scene.
 
While the rendering equation is a useful model for computer graphics some problems are easier to solve by a more generalized light model.
### The plenoptic function is also inspired by multi-faceted **insect eyes or lens arrays**.

<img src="https://user-images.githubusercontent.com/74843139/135706284-64636f81-b20d-429d-ba5c-2d55c5c6df02.png" width=150><img src="https://user-images.githubusercontent.com/74843139/134789523-accc48f7-988b-472f-8fbb-2dc7524a295a.png" width=550>

<small><i>Source: https://en.wikipedia.org/wiki/Compound_eye, Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>


<img src="https://user-images.githubusercontent.com/74843139/135736699-8ecb616b-9550-435c-b552-9825c702e7ec.png" width=350><img src="https://user-images.githubusercontent.com/74843139/135750276-9117a3c0-1736-44c2-85d5-697a02a38cbc.png" width=200><img src="https://user-images.githubusercontent.com/74843139/135749914-1c2b8ff7-7e18-4146-ab1a-b2accc62c4c2.png" width=200>

<small><i>Source: Rendering for Data Driven Computational Imaging, Tristan Swedish, https://www.blitznotes.org/ib/physics/waves.html, https://courses.lumenlearning.com/boundless-chemistry/chapter/the-nature-of-light/</i></small>

The plenoptic function describes the **degrees of freedom of a light ray with the paramters**: Irradiance, position, wavelength, time, angle, phase, polarization, and bounce. 

Light has the properties of waves. Like ocean waves, light waves have crests and troughs.
* The distance between one crest and the next, which is the same as the distance between one trough and the next, is called the **wavelength**. 
* **Wave phase** is the offset of a wave from a given point. When two waves cross paths, they either cancel each other out or compliment each other, depending on their phase. 
* **Irradiance** is the amount of light energy from one thing hitting a square meter of another each second. Photons that carry this energy have wavelengths from energetic X-rays and gamma rays to visible light to the infrared and radio. The unit of irradiance is the watt per square meter.
* **Polarization** 
* **Bounce** 
* The full equation is also **time dependent**.

    
## 5D and 4D Lightfields: capture and rendering (Andrey Gershun, 1936)
 If Vx, Vy, Vz are fixed, the plenoptic function describes a **panorama** at fixed viewpoint (Vx, Vy, Vz). A regular image with a limited field of view can be regarded as an incomplete plenoptic sample at a fixed viewpoint. As long as we stay outside the convex hull of an object or a scene, if we fix the location of the camera on a plane, we can use two parallel planes (u,v) and (s,t) to simplify the complete 5D plenoptic function to a 4D lightfield plenoptic function.
A **Light field** is a mathematical function of one or more variables whose range is a set of multidimensional vectors that describe the **amount of light flowing in every direction through every point in space***. It restricts the information to light outside the **convex hull** of the objects of interest. The 7D plenoptic function can under certain assumptions and relaxations simplify o a **4D light field**, which is easier to sample and operate on.
A hologram is a photographic recording of a light field, rather than an image formed by a lens. 

The magnitude of each light ray is given by the **radiance** and the space of all possible light rays is given by the five-dimensional plenoptic function. 
The 4D lightfield has **2D spatial (x,y) and 2D angular (u,v)** information that is captured by a plenoptic sensor.
* the **incident light field** Li(u, v, alpha, beta) describing the **irradiance** of light incident on objects in space
* the **radiant light field** Lr (u, v, alpha, beta) quantifying the irradiance created by an object
* time is an optional 5th dimension

### Capturing, storing and compressing static and dynamic light fields
One type uses an **array of micro-lenses** placed in front of an otherwise conventional image sensor to sense intensity, color, and directional information. Multi-camera arrays are another type. Compared to a traditional photo camera that only captures the intensity of the incident light, a light-field camera provides **angular information** for each pixel. 

In principle, this additional information allows 2D images to be reconstructed at a given focal plane, and hence a **depth map** can be computed.
While **special cameras and cameras arrangements** have been build to capture light fields it is also possible them with a conventional camera or smart phone under certain constraints (see [Crowdsampling the Plenoptic Function](#-crowdsampling-the-plenoptic-function--a-name--crowdsampling--)).

<img src="https://user-images.githubusercontent.com/74843139/134798665-82995833-8751-4944-a3dd-0ea99a376216.png" width=200><img src="https://user-images.githubusercontent.com/74843139/134801063-d3812c40-7696-499b-b168-10b6e44f4a21.png" width=200><img src="https://user-images.githubusercontent.com/74843139/134805766-295077e7-81ef-405b-9ad7-38e8c583a55f.png" width=250><img src="https://user-images.githubusercontent.com/74843139/135706121-e3a5d78a-9dfa-4247-b504-1ba32b1d11ed.png" width=150>
    
<small><i>Stanford light field camera; Right: Adobe (large) lens array, source https://cs.brown.edu/courses/csci1290/labs/lab_lightfields, "Lytro Illum", a discontinued commercially available light field camera</i></small>
 

#### Neural Scene representations
 <img src="https://user-images.githubusercontent.com/74843139/137575482-6c542c8d-e4d0-43f9-8638-9bcf58b58ee8.png" width=600>

<small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>

##### Networks, Acorn: Adaptive coordinate networks for neural scene representation (2021)
ACORN is a hybrid implicit-explicit neural representation that enables large-scale fitting of signals such as shapes or images. The Hybrid implicit-explicit network architecture and training strategy that adaptively allocates resources during training and inference based on the local complexity of a signal of interest. The approach uses a multiscale block-coordinate decomposition, similar to a quadtree or octree, that is optimized during training. The network architecture operates in two stages: using the bulk of the network parameters, a coordinate encoder generates a feature grid in a single forward pass. 
Then, hundreds or thousands of samples within each block can be efficiently evaluated using a lightweight **feature decoder**. With this hybrid implicit-explicit network architecture, we demonstrate the first experiments that fit gigapixel images to nearly 40 dB peak signal-to-noise ratio. Notably this represents an increase in scale of over 1000x compared to the resolution of previously demonstrated image-fitting experiments. 
The autors claim the approach is able to represent 3D shapes significantly faster and better than previous techniques; it reduces training times from days to hours or minutes and memory requirements by over an order of magnitude.
 
 <img src="https://user-images.githubusercontent.com/74843139/137810541-4cd98156-5085-4c0a-8edd-e2705605e5cc.png" width=400> <img src="https://user-images.githubusercontent.com/74843139/137811969-34734064-90b2-488e-8f0e-b096084e5d91.png" width=400

<small><i>https://www.computationalimaging.org/publications/acorn</i></small>

 
##### Multi-plane image (MPI) format and DeepMPI representation (2.5 D)
Deep image or video generation approaches that enable explicit or implicit control of scene properties such as illumination, camera parameters, pose, geometry, appearance, and semantic structure.
MPIs have the ability to produce high-quality novel views of complex scenes in real time and the view consistency that arises from a 3D scene representation (in contrast to neural rendering approaches that decode a separate view for each desired viewpoint).

<img src="https://user-images.githubusercontent.com/74843139/135738631-e9a72fde-c4d4-46f4-8c1e-1823c6607090.png" width=350><img src="https://user-images.githubusercontent.com/74843139/135738753-1067733d-2d60-45a8-bf9d-3b2dfab83f41.png" width=500>

<small><i>https://www.semanticscholar.org/paper/ACORN%3A-Adaptive-Coordinate-Networks-for-Neural-Martel-Lindell/2d0c07aa97b5b422c1ac512b1c184f412a19f28e/</i></small>
 
**DeepMPI** extends prior work on multiplane images (MPIs) to model viewing conditions that vary with time.

#### Compression
 TODO
 
### Novel (virtual) 2D view synthesis from plenoptic samples 
Synthesize plenoptic slices that can be interpolated to recover local regions of the full
plenoptic function.
Given a **dense sampling** of views, photorealistic novel views can be reconstructed by simple light field sample interpolation techniques. For novel view synthesis with **sparser view** sampling, the computer vision and graphics communities have made significant progress by predicting traditional geometry and appearance representations from observed images. The study of image-based rendering is motivated by a simple question: how do we use a finite set of images to reconstruct an infinite set of views.

View synthesis can be approached by either explicit estimation of scene geometry and color, or using coarser estimates of geometry to guide interpolation between captured views. 
One approach aims to **explicitly reconstruct the surface geometry** and the appearance on the surface from the observed sparse views, other approaches adopt volume-based representations to directly to model the appearance of the entire space and use volumetric rendering techniques to generate images for 2D displays. 
The raw samples of a light field are saved as disks. resolution large amounts of data

The Volume rendering technique known as ray marching. Ray marching is when you shoot out a ray from the observer (camera) through a 3D volume in space and ask a function: what is the color and opacity at this particular point in space? Neural rendering takes the next step by using a neural network to approximate this function. 
 
 <img src="https://user-images.githubusercontent.com/74843139/137583408-aa016bc7-b9c3-4d4b-94bf-1f570e7923c6.png" width=500>

 <small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
 
<img src="https://user-images.githubusercontent.com/74843139/134803822-801b99d7-5bd7-4ab3-8f12-0eba63eedcd5.png" width=300><img src="https://user-images.githubusercontent.com/74843139/135702022-93723d7a-fb76-4380-8150-7aaceff96757.png" width=300>

<small><i>Source: https://github.com/Arne-Petersen/Plenoptic-Simulation, A System for Acquiring, Processing, and Rendering Panoramic Light Field Stills for Virtual Reality</i></small>
 
<img src="https://user-images.githubusercontent.com/74843139/137581126-fbadc2ff-b2a4-438e-a671-ffa1f36509c0.png" width=500>

<small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
 
Light field rendering pushes the latter strategy to an extreme by using dense structured sampling of the lightfield to make re-construction guarantees independent of specific scene geometry. Most image based renering algorithms are designed to model static appearance, DeepMPI (Deep Multiplane Images), which further captures viewing condition dependent appearance. 

**Camera calibration** is often assumed to be prerequisite, while in practise, this information is rarely accessible, and requires to be pre-computed with conventional techniques, such as SfM.
 
#### 3d scene reconstruction and inverse and differential rendering 
##### Inverse rendering and differential rendering: explicitly reconstructing the scene
The key concept behind neural rendering approaches is that they are differentiable. A differentiable function is one whose derivative exists at each point in the domain. This is important because machine learning is basically the chain rule with extra steps: a differentiable rendering function can be learned with data, one gradient descent step at a time. Learning a rendering function statistically through data is fundamentally different from the classic rendering methods we described above, which calculate and extrapolate from the known laws of physics.

They can be classified into explicit and implicit representations. Explicit methods describe scenes as a collection of geometric primitives, such as triangles,
point-like primitives, or higher-order parametric surfaces.
 
<img src="https://user-images.githubusercontent.com/74843139/137583225-4c544c17-9082-43fe-812b-5ea2faef3660.png" width=300>

 <small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
 
One popular class of approaches uses mesh-based representations of scenes with either use [48] or view-dependent appearance. Differentiable rasterizers or pathtracers [22,30] can directly optimize mesh representations to reproduce a set of input images using gradient descent.
However, gradient-based mesh optimization based on image reprojection is often dicult, likely because of local minima or poor conditioning of the loss landscape. Furthermore, this strategy requires a template mesh with xed topology to be provided as an initialization before optimization [22], which is typically unavailable for unconstrained real-world scenes.

Inverse rendering aims to estimate physical attributes of a scene, e.g., reflectance, geometry, and lighting, from image(s).
Also called **Differentiable Rendering** it promises to close the loop between computer vision and graphics.
Differentiable Rendering promises to close the loop between computer Vision and Graphics.


#### Novel view synthesis with neural rendering: Volume Rendering with Radiance Fields
 In this problem, a neural network learns to render a scene from an arbitrary viewpoint. Slides 3 and 4 are figures from two great papers on this topic: one from Google Research [1] and the other from Facebook Reality Labs [2]. Both of these works use a volume rendering technique known as ray marching. Ray marching is when you shoot out a ray from the observer (camera) through a 3D volume in space and ask a function: what is the color and opacity at this particular point in space? Neural rendering takes the next step by using a neural network to approximate this function.

<img src="https://user-images.githubusercontent.com/74843139/137447390-2134a9a9-50a6-4911-93d7-f87f7114739f.png" width=300>

 <small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>


##### Neural Radiance Fields (NeRF): Representing Scenes as Neural Radiance Fields for View Synthesis (published 2020 Mildenhall et al.) 
Nerf implicit function encoded by A multilayer perceptron (MLP) 
A recent and popular **volumetric rendering technique** to generate images is Neural Radiance Fields (NeRF) due to its exceptional simplicity and performance for synthesising high-quality images of complex real-world scenes. 
The key idea in NeRF is to represent the entire volume space with a continuous function, parameterised by a **multi-layer perceptron (MLP)**, bypassing the need to discretise the space into voxel grids, which usually suffers from resolution constraints.
It allows real-time synthesis of photorealistic new views.

<img src="https://user-images.githubusercontent.com/74843139/137442095-16fac449-f819-4852-b749-16185cdec895.png" width=300><img src="https://user-images.githubusercontent.com/74843139/135739158-186170f4-ad7f-4734-aceb-24a7db7c02dd.png" width=400> <img src="https://user-images.githubusercontent.com/74843139/137458615-ca69a159-670f-474f-ae14-2171ec577898.png" width=400>

 <small><i>Advances in Neural Rendering, 2021, ttps://www.neuralrender.com/</i></small>

<img src="https://user-images.githubusercontent.com/74843139/137470543-bb2af13d-b18d-49cf-bc2d-a0cdd88941a9.png" width=400><img src="ttps://user-images.githubusercontent.com/74843139/137576118-dcc2167e-5809-4099-83b2-07abf1f39d61.png" width=400>

 <small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>

**Neural volume rendering** refers to methods that generate images or video by tracing a ray into the scene and taking an integral of some sort over the length of the ray. Typically a neural network like a multi-layer perceptron (MLP) encodes a function from the 3D coordinates on the ray to quantities like **density and color**, which are integrated to yield an image. One of the reasons NeRF is able to render with great detail is because it encodes a 3D point and associated view direction on a ray using **periodic activation functions, i.e., Fourier Features**. 
The impact of the NeRF paper lies in its brutal simplicity: just an MLP taking in a 5D coordinate and outputting density and color yields photoreastic results.
The inital model had limitions: Training and rendering is slow and it can only represent static scenes. It “bakes in” lighting. A trained NeRF representation does not generalize to other scenes or objects. All of these problems have since developed further, there are even realtime nerfs now.  
A good overview can be found in "NeRF Explosion 2020", https://dellaert.github.io/NeRF.
 
https://user-images.githubusercontent.com/74843139/135747420-4d91bc80-2893-44a4-8d32-16bf7024b4f2.mp4
 
<img src="https://user-images.githubusercontent.com/74843139/137474885-432d596d-3820-4932-854e-61652dfae9a2.png" width=400><img src="https://user-images.githubusercontent.com/74843139/137718556-57c12830-74a9-447e-adec-c65624cd21d0.png" width=400>
 
<small><i>https://dellaert.github.io/NeRF/</i></small>
 <small><i>https://towardsdatascience.com/nerf-and-what-happens-when-graphics-becomes-differentiable-88a617561b5d</i></small>
 
 A deeper integration of graphics knowledge into the network is possible based
on differentiable graphics modules. Such a differentiable module
can for example implement a complete computer graphics renderer, a 3D rotation, or an illumination model. Such components add a physically
inspired inductive bias to the network, while still allowing
for end-to-end training via backpropagation. This can be used to
analytically enforce a truth about the world in the network structure,
frees up network capacity, and leads to better generalization,
especially if only limited training data is available.

<img src="https://user-images.githubusercontent.com/74843139/137445499-1d2f3bae-9aab-46b5-81c2-9f4c4d3fa9e3.png" width=400>

 <small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
 
###### Crowdsampling the Plenoptic Function with NeRF (published 2020)
Given a large number of **tourist photos taken at different times of day**, this machine learning based approach learns to construct a continuous set of light fields and to synthesize novel views capturing all-times-of-day scene appearance. achieve convincing changes across a variety of times of day and lighting conditions.
mask out transient objects such as people and cars during training and evaluation.

<img src="https://user-images.githubusercontent.com/74843139/134799704-32fd77d0-f71e-47aa-b763-fcef4af17e9d.png" width=500>
<img src="https://research.cs.cornell.edu/crowdplenoptic/teaser/coeur_teaser.gif)![image](https://research.cs.cornell.edu/crowdplenoptic/teaser/trevi_teaser.gif" width=500>
 
 
<img src="https://research.cs.cornell.edu/crowdplenoptic/teaser/rock_teaser.gif" width=500><img src="https://research.cs.cornell.edu/crowdplenoptic/teaser/pantheon_teaser.gif" width=500>
<small><i>Source: https://www.semanticscholar.org/paper/Crowdsampling-the-Plenoptic-Function-Li-Xian</i></small>
 
          
<img src="https://user-images.githubusercontent.com/74843139/135809964-0501661b-2cfa-4ab2-a818-0f60bd6fd152.png" width=500>

<small><i>Crowdsampling the Plenoptic Function, 2020</i></small>
                                                                                                                
unsupervised manner. The approach takes unstructured Internet photos spanning
some range of time-varying appearance in a scene and learns how to reconstruct a
plenoptic slice, a representation of the light field that respects temporal structure
in the plenoptic function when interpolated over time|for each of the viewing
conditions captured in our input data. By designing our model to preserve the
structure of real plenoptic functions, we force it to learn time-varying phenomena
like the motion of shadows according to sun position. This lets us, for example,
recover plenoptic slices for images taken at different times of day (Figure 1,
bottom row) and interpolate between them to observe how shadows move as
the day progresses (best seen in our supplemental video). In eect, we learn a
representation of the scene that can produce high-quality views from a continuum
of viewpoints and viewing conditions that vary with time.
Our work makes three key contributions: first, a representation, called a
DeepMPI, for neural rendering that extends prior work on multiplane images
(MPIs) [68] to model viewing conditions that vary with time; second, a method
for training DeepMPIs on sparse, unstructured crowdsampled data that is unreg-
1 [1] describes the plenoptic function as 7D, but we can reduce this to a 4D color lightfield supplemented by time by applying the later observations of [33].
Crowdsampling the Plenoptic Function 3 istered in time
                                                                                                                
<img src="https://user-images.githubusercontent.com/74843139/135751323-ef8582a0-575d-41fb-9a40-861fbbbd35d3.png" width=500>
                                                                                                                
<small><i>nerf in the wild</i></small>

<img src="https://user-images.githubusercontent.com/74843139/135836081-9dacc9ba-0ddb-4665-8e54-359b1e500dfa.png" width=500>

<small><i>nerf in the wild</i></small>
                                                                                                                
Unfortunately, there are two major drawbacks with VGGNet:     It is painfully slow to train.
The network architecture weights themselves are quite large (in terms of disk/bandwidth).
Due to its depth and number of fully-connected nodes, VGG is over 533MB for VGG16 and 574MB for VGG19. This makes deploying VGG a tiresome task.
We still use VGG in many deep learning image classification problems; however, smaller network architectures are often more desirable (such as SqueezeNet, GoogLeNet, etc.).
 
<small><i>https://www.pyimagesearch.com/2017/03/20/imagenet-vggnet-resnet-inception-xception-keras/</i></small>

For the future the autors envision enabling even larger changes in viewpoint and illumination, including 4D walkthroughs of large-scale scenes in the future.
                                                                                                                
#### Towards Instant 3D Capture (with a cell phone): Nerfies
<img src="https://user-images.githubusercontent.com/74843139/137580135-e9373469-3c76-42ff-b44c-7b11878e776c.png" width=500>


 <small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
                                                                                                                
### Relighting with 4D Incident Light Fields
It is possible to **re-light and de-light real objects** illuminated by a 4D incident light field, representing the illumination of an environment. By exploiting the richness in angular and spatial variation of the light field, objects can be relit with a high degree of realism.


<img src="https://user-images.githubusercontent.com/74843139/135739588-00789dba-9ddc-45a8-bc44-5a9f5c0fc7da.png" width=500>

<small><i>https://en.wikipedia.org/wiki/Light_stage</i></small>
 
<img src="https://user-images.githubusercontent.com/74843139/137584537-473c169d-bfeb-4f8e-a6ef-a5b80d7e71f1.png" width=500><img src="https://user-images.githubusercontent.com/74843139/137585106-e23fda39-222b-45ae-a3e0-f6bf6b5e6ef0.png" width=500>
<small><i></i></small>
                                                                                                                
#### Relighting with NeRF
Another dimension in which NeRF-style methods have been augmented is in how to deal with lighting, typically through latent codes that can be used to re-light a scene. 
NeRF-W was one of the first follow-up works on NeRF, and optimizes a latent appearance code to enable learning a neural scene representation from less controlled multi-view collections.

Neural Reflectance Fields improve on NeRF by adding a local reflection model in addition to density. It yields impressive relighting results, albeit from single point light sources. NeRV uses a second “visibility” MLP to support arbitrary environment lighting and “one-bounce” indirect illumination.
<img src="https://user-images.githubusercontent.com/74843139/137584259-4e0a264d-8758-4775-aa75-5ea72c18e585.png " width=500>

                                                                                                                
<img src="https://user-images.githubusercontent.com/74843139/137581185-52635c88-28ce-4997-8bb6-8ac8e7dc108d.png" width=500><img src="https://user-images.githubusercontent.com/74843139/137581670-950f5679-921c-48db-b0c1-5dff7997d4ef.png" width=500>

<small><i>https://en.wikipedia.org/wiki/Light_stage</i></small>

<img src="https://user-images.githubusercontent.com/74843139/137583652-53fa4751-18cc-45f9-a569-6d7505111c1c.png" width=500><img src="https://user-images.githubusercontent.com/74843139/137583799-8fd93d70-1930-47b1-b313-cc77c92ec32d.png" width=500>

<small><i>/i></small>
                                                                                                                
<img src="https://user-images.githubusercontent.com/74843139/137583877-47f21587-0bdb-412c-b035-1d906ae65d85.png" width=500>
 ##### NeRD: Neural Reflectance Decomposition from Image Collections
 NeRD is a  method that can decompose image collections from multiple views taken under varying or fixed illumination conditions. The object can be rotated, or the camera can turn around the object. The result is a neural volume with an explicit representation of the appearance and illumination in the form of the BRDF and Spherical Gaussian (SG) environment illumination.
 
 ![image](https://user-images.githubusercontent.com/74843139/137691538-a069ec6c-86f2-459d-b0e0-a53966399245.png)

 <small><i>Source: Advances in Neural Rendering, https://www.neuralrender.com/</i></small>
  
 #### Editable NeRFs
<img src="https://user-images.githubusercontent.com/74843139/137584064-9dc8bd13-8c82-48b2-9733-bc72f8cf11cc.png" width=500><img src="https://user-images.githubusercontent.com/74843139/137584284-dd443eb8-82c3-4628-a518-ab133c5d8a69.png" width=500>

<small><i>/i></small>
                                                                                                                
## Temporally Coded Imaging: Time Resolved Imaging (TRI) or Time-of-Flight (ToF) Imaging and LIDAR (1961)
ToF refers to the use of the **speed of light or even sound** to determine distance, as it measures the time it takes light to leave a device, bounce off an object or plane, and return to the device, all divided by two reveals The distance from the device to the object or plane.
ToF applications create "depth maps" based on light detection, usually with a standard RGB camera, and the advantage that ToF offers compared to LiDAR is that ToF requires less specialized equipment so that it can be used with smaller, cheaper devices.

<img src="https://user-images.githubusercontent.com/74843139/134803482-e283e016-a50e-4ae9-aca7-d35128ba9554.png" width=500><img src="(https://user-images.githubusercontent.com/74843139/134806495-5d40deff-328c-42b5-ada4-2035399abf1f.png" width=200>
                                                                                                                 
<small><i>Source: CVPR 2019 Data-Driven Computational Imaging</i></small>
 
**LiDAR**<a name="lidar"> stands for **light detection and ranging**, and has been around since 1961. It uses lasers to ping off objects and return to the source of the laser, measuring distance by timing the travel, or flight, of the light pulse. Time-of-flight cameras on smartphones tend to be used to improve focus accuracy and speed, in particular better low-light focus.
It's used for self-driving cars, or assisted driving. It's used for robotics and drones. Augmented reality headsets like the HoloLens 2 have similar tech, mapping out room spaces before layering 3D virtual objects into them
 
Lidar can be used to mesh out 3D objects and rooms and layer photo imagery on top, a technique called photogrammetry. That could be the next wave of capture tech for practical uses
     
# Related fields
## Photogrammetry (first mentioned in 1867)
Photogrammetry is the science of **reconstructing objects and environments that exist in the physical world** through photographs. The technique involves stitching together large collections of overlapping photographs to create **topographical maps, point clouds** and may also produce **2D and 3D digital models**. First mentioned in 1867 it predates digital photography significantly.

Photogrammetry data is usually captured with a **single moving conventional still frame camera** or uses **aerial data**. Sometimes it is combined with LIDAR data for depth information. Visibility constraints such as rain, occlusion or dense vegetation can block the camera's line of sight or limit light required for good result
See [Lidar](#lidar) for alternatives. Lighting is backed into these reconstructed scenes.
                        
<img src="https://user-images.githubusercontent.com/74843139/134804512-4c7ab394-319e-4952-895c-405799bf5073.png" width=300>
 
<small><i>Source: http://www.aamspi.com/services/aerial-photogrammetry/</i></small>
 
A historical overview can be found at https://alicevision.org/#history.
                                                                                                                
## Computional imaging for machines (not human observer)
### Lensless cameras (mid-1990s): Optical images for a computer (not for a human oberserver)
The basic design of a camera has remained unchanged for centuries. To acquire an image, light from the scene under view is focused onto a photosensitive surface using a lens. The primary task of a lens in a camera is to shape the incoming light wavefront so that it creates a focused
Lenses introduce a number of limitations, specifically all traditional cameras have limited depth of field. 

Cameras also end up being thick due to the lens complexity and the large distance required between the lens and sensor to achieve focus.
Lensless imaging systems dispense with a lens by using other optical elements to manipulate the incoming light. The sensor records the intensity of the manipulated light, which may not appear as a focused image. However, when the system is designed correctly, the image can be recovered
from the sensor measurements with the help of a computational algorithm.
The simplest but inefficient lensless imaging system is the pinhole camera, coded aperture cameras improve the light efficiency using a mask with an array of
pinholes.

<img src="https://user-images.githubusercontent.com/74843139/135749276-a0078caa-c376-45e2-a75b-a991629991f2.png" width=500>
 
<small><i> https://zeenews.india.com/india/iit-m-us-researchers-develop-algorithms-to-get-clearer-images-from-futuristic-lensless-cameras-2362619.html</i></small>


###  Simultaneous localization and mapping: SLAM (1986). Generating machine readable near realtime maps
Achieving real-time perception is critical to developing a fully autonomous system that can sense, navigate, and interact with its environment. Perception tasks such as online 3D reconstruction and mapping.

<img src="https://user-images.githubusercontent.com/74843139/135749450-762580f6-30d8-478c-b45c-0372756dba53.png" width=500>
 
<small><i>https://medium.com/@hurmh92/autonomous-driving-slam-and-3d-mapping-robot-e3cca3c52e95</i></small>

# Conclusion
 Over the past year (2020), we’ve learned how to make the rendering process differentiable, and turn it into a deep learning module. This sparks the imagination, because the deep learning motto is: “If it’s differentiable, we can learn through it”. If we know how to differentially go from 3D to 2D, it means we can use deep learning and backpropagation to go back from 2D to 3D as well.
 
With neural rendering we no longer need to physically model the scene and simulate the light transport, as this knowledge is now stored implicitly inside the weights of a neural network. The compute required to render an image is also no longer tied to the complexity of the scene (the number of objects, lights, and materials), but rather the size of the neural network. 
                                                                                                                
Neural rendering has already enabled applications that were **previously intractable**, such as rendering
of digital avatars without any manual modeling. Neural rendering could have a profound impact in making complex
photo and video editing tasks accessible to a much broader audience. 
 
This is no longer a neural network that is predicting physics. This is physics (or optics) plugged on top of a neural network inside a PyTorch engine. We have now a differentiable simulation of the real world (harnessing the power of computer graphics) on top of a neural representation of it.
 
<img src="https://user-images.githubusercontent.com/74843139/137446172-b1f57e66-fed8-4e9a-bba0-6ff0e686e54a.png" width=500>
 
<small><i>https://medium.com/@hurmh92/autonomous-driving-slam-and-3d-mapping-robot-e3cca3c52e95</i></small>

# Important concepts
 * *DeepMPI*<a name="deepmpi"> Deep Multiplane Images
 * *Light field* is a mathematical function of one or more variables whose range is a set of multidimensional vectors that describe the **amount of light flowing in every direction through every point in space***
 * *Radiance*<a name="radiance"> represents the ray strength, measuring the combined angular and spatial power densities. Radiance can be used to indicate how much of the power emitted by the light source that is reflected, transmitted or absorbed by a surface will be captured by a camera facing that surface from a specified angle of view.
  radiance L of a surface is **emitted flux per unit** of foreshortened area (from Lambert’s Law) per unit solid angle
 * *Irradiance* is the radiant flux received by the detector area. The unit of irradiance is W/m2. The irradiance E of a surface is defined as the incident radiant flux density
    
# Recommended reading
## Neural Rendering and Nerf explosion
* Advances in Neural Rendering https://s2021.siggraph.org/presentation/?id=gensub_303&sess=sess152, 5 hours video class https://www.neuralrender.com/
* EUROGRAPHICS 2020, State of the Art on Neural Rendering
* Great intro: "NeRF and What Happens When Graphics Becomes Differentiable", https://towardsdatascience.com/nerf-and-what-happens-when-graphics-becomes-differentiable-88a617561b5d
* https://www.techeblog.com/google-nerf-in-the-wild-2d-photo-3d-model/
* https://dellaert.github.io/NeRF,Frank Dellaert, NeRF Explosion 2020
* https://www.matthewtancik.com/nerf
* https://paperswithcode.com/task/neural-rendering
* https://medium.com/mlearning-ai/what-is-neural-rendering-e25371afc771
* https://www.lightfieldlab.com Lightfield displays

* Ben Mildenhall's research page, https://bmild.github.io/
* https://slideslive.com/38939970/a-i-for-3d-content-creation
* https://github.com/facebookresearch/pytorch3d/blob/12514463830edd007a76226301b62593f62ea279/projects/nerf/README.md
* NeRD: Neural Reflectance Decomposition from Image Collections, https://markboss.me/publication/2021-nerd/
* https://github.com/Kai-46/nerfplusplus
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling Plenoptic Function
 
 ## Other
* *Computational Imaging* By Ayush Bhandari, Achuta Kadambi and Ramesh Raskar, A comprehensive and up-to-date textbook and reference for computational imaging, which combines vision, graphics, signal processing, and optics, http://compimagingbook.com
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling the Plenoptic Function
* Data-Driven Computational Imaging http://ciml.media.mit.edu, CVPR 2019
* https://cseweb.ucsd.edu/~viscomp/projects/LF/
* EUROGRAPHICS 2006 Tutorial "Inverse Rendering: From Concept to Applications", https://diglib.eg.org/bitstream/handle/10.2312/egt.20061062.0399-0547/0399-0547.pdf
* *CVPR 2021 Tutorial on Physics-Based Differentiable Rendering*, June 20, 2021,  https://diff-render.org
* https://raytrix.de Realtime Plenoptic Metrology
* https://github.com/Arne-Petersen/Plenoptic-Simulation, "Simulation of Plenoptic Cameras", 3DTV Conference, 2018. DOI: 10.1109/3DTV.2018.8478432 Preprint
* https://en.wikipedia.org/wiki/Light_field 
 *https://www.cnet.com/tech/mobile/lidar-is-one-of-the-iphone-ipad-coolest-tricks-its-only-getting-better/
* https://www.americanscientist.org/article/imaging-without-lenses
* https://github.com/tensorflow/graphics
* https://arxiv.org/abs/2102.07064, 2021 NeRF--: Neural Radiance Fields Without Known Camera Parameters Zirui Wang, Shangzhe Wu, Weidi Xie, Min Chen, Victor Adrian Prisacariu
* https://www.pauldebevec.com lots of ground breaking research and their github https://github.com/augmentedperception
* https://github.com/amusi/ICCV2021-Papers-with-Code#NeRF ICCV'21 paper
* ADOP: Approximate Differentiable One-Pixel Point Rendering, abs: https://t.co/npOqsAstAx https://t.co/LE4ZdckQPO
 
