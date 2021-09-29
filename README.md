#  Sehender Raum / Seeing Space (WIP)
## Notes about capturing, rendering and digitally reconstruction the world
When I learned about (traditional) computer graphics and photogrammetry I missed the big picture about how all the pieces connect: with hardware, physics and machine learning aspects. It makes it harder to understand recent research and its meaning for the field. Rendering 3D models from 2D images remains a challenging problem but huge progress has been made since I first became interested in the opic 20 years ago.

Catching up with newer research in image based rendering: A TLDR on how traditional computer graphics fits with computer vision, machine learning and capture hardware.

- [Data-Driven Computational Imaging](#data-driven-computational-imaging)
  * [Image-based rendering: Plenoptic function and capture](#image-based-rendering--plenoptic-function-and-capture)
    + [The 5D and 7D Plenoptic function (Adelson and Bergen, 1991)](#the-5d-and-7d-plenoptic-function--adelson-and-bergen--1991-)
    + [4D Lightfields: capture and rendering (Adelson and Bergen, 1991)](#4d-lightfields--capture-and-rendering--adelson-and-bergen--1991-)
      - [Capturing static and dynamic light fields](#capturing-static-and-dynamic-light-fields)
      - [Novel (virtual) 2D view synthesis](#novel--virtual--2d-view-synthesis)
        * [Neural Radiance Fields (NeRF): Representing Scenes as Neural Radiance Fields for View Synthesis (published 2020 Mildenhall et al.)](#neural-radiance-fields--nerf---representing-scenes-as-neural-radiance-fields-for-view-synthesis--published-2020-mildenhall-et-al-)
      - [Relighting with 4D Incident Light Fields](#relighting-with-4d-incident-light-fields)
    + [Temporally Coded Imaging: Time Resolved Imaging (TRI) or Time-of-Flight (ToF) Imaging](#temporally-coded-imaging--time-resolved-imaging--tri--or-time-of-flight--tof--imaging)
  * [3d scene reconstruction and inverse and differential rendering](#3d-scene-reconstruction-and-inverse-and-differential-rendering)
    + [Photogrammetry (first mentioned in 1867)](#photogrammetry--first-mentioned-in-1867-)
    + [Simultaneous localization and mapping: SLAM (1986)](#simultaneous-localization-and-mapping--slam--1986-)
    + [Visual Sensing Using Machine Learning](#visual-sensing-using-machine-learning)
    + [Inverse rendering and differential rendering: Analysis by Synthesis](#inverse-rendering-and-differential-rendering--analysis-by-synthesis)
- [Important concepts](#important-concepts)
- [Recommended reading](#recommended-reading)
  * [Image-based rendering](#image-based-rendering)
  * [Visual Sensing Using Machine Learning](#visual-sensing-using-machine-learning-1)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


## Data-Driven Computational Imaging
### Image-based rendering: Plenoptic function and capture
#### The Plenoptic function (Adelson and Bergen, 1991)
The world as we see it using our eyes is a continuous three-dimensional function of the spatial coordinates. To generate photo-realistic views of a real-world scene from any viewpoint, it not only requires to understand the 3D scene geometry, but also to model complex viewpoint-dependent appearance resulting of light transport phenomena. A photograph is a two-dimensional map of the “no of photons” that map from the three-dimensional scene.

A point in the scene is imaged by measuring the emitted and reflected light that converges on the sensor plane. **Radiance (L)** represents the ray strength, measuring the combined angular and spatial power densities. Radiance can be used to indicate how much of the power emitted by the light source that is reflected, transmitted or absorbed by a surface will be captured by a camera facing that surface from a specified angle of view. 

The **rendering equation** (publisheed in 1986) describes physical light transport for a single camera or the human vision

<img src="https://user-images.githubusercontent.com/74843139/134788604-b920d1c9-bb65-408a-9eb1-eab3ea1d4408.png" width=300><img src="https://user-images.githubusercontent.com/74843139/134789211-04ab96e8-04e5-4571-8437-8907bd98e58b.png" width=300>
 
<small><i>Source: Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>
 
While the rendering equation is a useful model for computer graphics some problems are easier to solve by a more generalized light model.
The **plenoptic function** is also inspired by multi-faceted **insect eyes or camera arrays**.
<img src="https://user-images.githubusercontent.com/74843139/134789523-accc48f7-988b-472f-8fbb-2dc7524a295a.png" width=550>
    
<small><i>Source: Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>
 
<img src="https://user-images.githubusercontent.com/74843139/134788591-e65ba01b-3dda-407b-9f91-712af9f224e8.png" width=450>
<small><i>Source: Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>
 
    
The plenoptic function describes the degrees of freedom of a light ray with the paramters: Irradiance, position, wavelength, time, angle, phase, polarization, and bounce. 
Light has the properties of waves. Like ocean waves, light waves have crests and troughs.
* The distance between one crest and the next, which is the same as the distance between one trough and the next, is called the **wavelength**. 
* **Wave phase** is the offset of a wave from a given point. When two waves cross paths, they either cancel each other out or compliment each other, depending on their phase. 
* **Irradiance** is the amount of light energy from one thing hitting a square meter of another each second. Photons that carry this energy have wavelengths from energetic X-rays and gamma rays to visible light to the infrared and radio.
* The full equation is also **time dependent**.
* Polarization
    
#### 4D Lightfields: capture and rendering (Adelson and Bergen, 1991)
A **Light field** is a mathematical function of one or more variables whose range is a set of multidimensional vectors that describe the **amount of light flowing in every direction through every point in space***. It restricts the information to light outside the **convex hull** of the objects of interest. The magnitude of each ray is given by the **radiance** and the space of all possible light rays is given by the five-dimensional plenoptic function. The 7D plenoptic function can under certain assumptions and relaxations simplify o a **4D light field**, which is easier to sample and operate on.
 
The 4D lightfield has **2D spatial (x,y) and 2D angular (u,v)** information that is captured by a plenoptic sensor.
• the **incident light field** Li(u, v, alpha, beta) describing the **irradiance** of light incident on objects in space
• the **radiant light field** Lr (u, v, alpha, beta) quantifying the irradiance created by an object


##### Capturing static and dynamic light fields
Compared to a traditional photo camera that only captures the intensity of the incident light, a light-field camera provides **angular information** for each pixel. In principle, this additional information allows 2D images to be reconstructed at a given focal plane, and hence a depth map can be computed.
While **special cameras and cameras arrangements** have been build to capture light fields it is also possible them with a conventional camera or smart phone under certain constraints (see [Crowdsampling the Plenoptic Function](#-crowdsampling-the-plenoptic-function--a-name--crowdsampling--)).

<img src="https://user-images.githubusercontent.com/74843139/134798665-82995833-8751-4944-a3dd-0ea99a376216.png" width=250><img src="https://user-images.githubusercontent.com/74843139/134801063-d3812c40-7696-499b-b168-10b6e44f4a21.png" width=250><img src="https://user-images.githubusercontent.com/74843139/134805766-295077e7-81ef-405b-9ad7-38e8c583a55f.png" width=250>
    
<small><i>Stanford light field camera; Right: Adobe (large) lens array, source https://cs.brown.edu/courses/csci1290/labs/lab_lightfields, "Lytro Illum", a discontinued commercially available light field camera</i></small>
 
 
##### Novel (virtual) 2D view synthesis
Unfortunately, it is not feasible in practice to physically measure a densely sampled plenoptic function. As an alternative, Novel View Synthesis aims to approximate such a dense light field from only sparse observations, such as a small set of images captured from diverse viewpoints. Multiple methods for novel view synthesis exists. One approach aims to explicitly reconstruct the surface geometry and the appearance on the surface from the observed sparse views, other approaches adopt volume-based representations to directly model the appearance of the entire space and use volumetric rendering techniques to generate images for 2D displays. 
The raw samples of a light field are saved as disks. resolution large amounts of data
 
<img src="https://user-images.githubusercontent.com/74843139/134803822-801b99d7-5bd7-4ab3-8f12-0eba63eedcd5.png" width=300>
<small><i>Source: A System for Acquiring, Processing, and Rendering Panoramic Light Field Stills for Virtual Reality</i></small>
 
View synthesis can be approached by either explicit estimation of scene geometry and color, or using coarser estimates of geometry to guide interpolation between captured views. Light field rendering pushes the latter strategy to an extreme by using dense structured sampling of the lightfield to make re-construction guarantees independent of specific scene geometry. Most IBR algorithms are designed to model static appearance, DeepMPI (Deep Multiplane Images), which further captures viewing condition dependent appearance. 
camera calibration is often assumed to be prerequisite, while in practise, this information is rarely accessible, and requires to be pre-computed with conventional techniques, such as SfM.
 
###### Neural Radiance Fields (NeRF): Representing Scenes as Neural Radiance Fields for View Synthesis (published 2020 Mildenhall et al.) 
A popular volumetric rendering technique to generate images is Neural Radiance Fields (NeRF) due to its exceptional simplicity and performance for synthesising high-quality images of complex real-world scenes. The key idea in NeRF is to represent the entire volume space with a continuous function, parameterised by a multi-layer perceptron (MLP), bypassing the need to discretise the space into voxel grids, which usually suffers from resolution constraints.
 
####### Crowdsampling the Plenoptic Function (published 2020)
Given a large number of tourist photos taken at different times of day, this machine learning based approach learns to construct a continuous set of light fields and to synthesize novel views capturing all-times-of-day scene appearance. achieve convincing changes across
 
<img src="https://user-images.githubusercontent.com/74843139/134799704-32fd77d0-f71e-47aa-b763-fcef4af17e9d.png" width=500>
<small><i>Source: https://www.semanticscholar.org/paper/Crowdsampling-the-Plenoptic-Function-Li-Xian</i></small>
 
##### Relighting with 4D Incident Light Fields
#### Temporally Coded Imaging: Time Resolved Imaging (TRI) or Time-of-Flight (ToF) Imaging
ToF refers to the use of the **speed of light or even sound** to determine distance, as it measures the time it takes light to leave a device, bounce off an object or plane, and return to the device, all divided by two reveals The distance from the device to the object or plane.
ToF applications create "depth maps" based on light detection, usually with a standard RGB camera, and the advantage that ToF offers compared to LiDAR is that ToF requires less specialized equipment so that it can be used with smaller, cheaper devices.

<img src="https://user-images.githubusercontent.com/74843139/134803482-e283e016-a50e-4ae9-aca7-d35128ba9554.png" width=500>
<small><i>Source: Rendering for Data Driven Computational Imaging, Tristan Swedish</i></small>
    
<img src="(https://user-images.githubusercontent.com/74843139/134806495-5d40deff-328c-42b5-ada4-2035399abf1f.png" width=200>
<small><i>Source: CVPR 2019 Data-Driven Computational Imaging</i></small>
 
     
### 3d scene reconstruction and inverse and differential rendering 
#### Photogrammetry (first mentioned in 1867)
Photogrammetry is the science of **reconstructing objects and environments that exist in the physical world** through photographs. The technique involves stitching together large collections of overlapping photographs to create **topographical maps, point clouds** and may also produce **2D and 3D digital models**. First mentioned in 1867 it predates digital photography significantly. 

Photogrametry data is usually captured with a **single moving conventional still frame camera** or uses **aerial data**. Sometimes it is combined with LIDAR data for depth information. Visibility constraints such as rain, occlusion or dense vegetation can block the camera's line of sight or limit light required for good results.
 
<img src="https://user-images.githubusercontent.com/74843139/134804512-4c7ab394-319e-4952-895c-405799bf5073.png" width=300>
    
<small><i>Source: http://www.aamspi.com/services/aerial-photogrammetry/</i></small>
####  Simultaneous localization and mapping: SLAM (1986)
Achieving real-time perception is critical to developing a fully autonomous system that can sense, navigate, and interact with its environment. Perception tasks such as online 3D reconstruction and mapping 
 
#### Visual Sensing Using Machine Learning 
#### Inverse rendering and differential rendering: Analysis by Synthesis
Inverse rendering aims to estimate physical attributes of a scene, e.g., reflectance, geometry, and lighting, from image(s).
Also called **Differentiable Rendering** it promises to close the loop between computer vision and graphics.

## Important concepts
 * *DeepMPI*<a name="deepmpi"> Deep Multiplane Images
 * *Light field* is a mathematical function of one or more variables whose range is a set of multidimensional vectors that describe the **amount of light flowing in every direction through every point in space***
 * *Radiance*<a name="radiance"> represents the ray strength, measuring the combined angular and spatial power densities. Radiance can be used to indicate how much of the power emitted by the light source that is reflected, transmitted or absorbed by a surface will be captured by a camera facing that surface from a specified angle of view.
 * The irradiance E of a surface is defined as the incident radiant flux density, and has units of W· m−2. Meanwhile, radiance L of a surface is emitted flux per unit of foreshortened area (from Lambert’s Law) per unit solid angle W· m−2 · sr−1.
    
## Recommended reading

### Image-based rendering
* *Computational Imaging* By Ayush Bhandari, Achuta Kadambi and Ramesh Raskar, A comprehensive and up-to-date textbook and reference for computational imaging, which combines vision, graphics, signal processing, and optics, http://compimagingbook.com
* *CVPR 2021 Tutorial on Physics-Based Differentiable Rendering*, June 20, 2021,  https://diff-render.org
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling the Plenoptic Function
* Data-Driven Computational Imaging http://ciml.media.mit.edu, CVPR 2019
* https://cseweb.ucsd.edu/~viscomp/projects/LF/


 ### Visual Sensing Using Machine Learning
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling Plenoptic Function
* https://github.com/tensorflow/graphics
* https://arxiv.org/abs/2102.07064, 2021 NeRF--: Neural Radiance Fields Without Known Camera Parameters Zirui Wang, Shangzhe Wu, Weidi Xie, Min Chen, Victor Adrian Prisacariu
* https://www.techeblog.com/google-nerf-in-the-wild-2d-photo-3d-model/
* https://dellaert.github.io/NeRF
