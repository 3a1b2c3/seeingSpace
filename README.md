#  Sehender Raum / Seeing Space (WIP)
## Notes about capturing, rendering and digitally reconstruction the world
When I learned about (traditional) computer graphics I think i missed the big picture about how all the pieces connect with hardware, physics and machine learning aspects. 
It makes it harder to understand recent research and its meaning for the field.
20 years after Photogrammetry got popular we see amazing progress.

A TLDR on how computer graphics fits with newer research, computer vision, machine learning and hardware.


## Data-Driven Computational Imaging
### Image-based rendering: Plenoptic function and cameras, light fields
#### Plenoptic function
The **rendering equation** describes physical light transport for a single camera or the human vision. 

<img src="https://user-images.githubusercontent.com/74843139/134788604-b920d1c9-bb65-408a-9eb1-eab3ea1d4408.png" width=300>
Source: Rendering for Data Driven Computational Imaging, Tristan Swedish

<img src="https://user-images.githubusercontent.com/74843139/134789211-04ab96e8-04e5-4571-8437-8907bd98e58b.png" width=300>

<img src="https://user-images.githubusercontent.com/74843139/134788646-69861df7-4656-449e-adb4-210c8e0307ff.png" width=300>
Source: Rendering for Data Driven Computational Imaging, Tristan Swedish

While it is a useful model for graphics some problems are easier to understand with an integration of the model.
Inspired by insect eyes or camera arrays the **plenoptic function** generalizes this idea:

<img src="https://user-images.githubusercontent.com/74843139/134789523-accc48f7-988b-472f-8fbb-2dc7524a295a.png" width=450>
Source: Rendering for Data Driven Computational Imaging, Tristan Swedish

The full equation is also time dependent. 
**Radiance** represents the ray strength, measuring the combined angular and spatial power densities. Radiance can be used to indicate how much of the power emitted by the light source that is reflected, transmitted or absorbed by a surface will be captured by a camera facing that surface from a specified angle of view.


#### Lightfields: View interpolation 

A **Light field** is a mathematical function of one or more variables whose range is a set of multidimensional vectors that describe the amount of light flowing in every direction through every point in space. The magnitude of each ray is given by the radiance and the space of all possible light rays is given by the five-dimensional plenoptic function. The 7D plenoptic function under certain assumptions and relaxations simplifies to a 4D light field, which is easier to sample and operate on.

While special cameras and cameras arrangements have been build to capture light fields it is also possible them with a conventional camera in some cases (see Crowdsampling the Plenoptic Function).

<img src="https://user-images.githubusercontent.com/74843139/134788591-e65ba01b-3dda-407b-9f91-712af9f224e8.png" width=450>
Source: Rendering for Data Driven Computational Imaging, Tristan Swedish


#### Photogrammetry

### 3d scene reconstruction and inverse rendering
#### Visual Sensing Using Machine Learning
##### Neural Radiance Fields (NeRF) 
###### Crowdsampling the Plenoptic Function
#### Inverse rendering: Analysis by Synthesis
Differentiable Rendering promises to close the loop between computer vision and graphics.

## Recommended reading
### Image-based rendering
* *Computational Imaging* By Ayush Bhandari, Achuta Kadambi and Ramesh Raskar, A comprehensive and up-to-date textbook and reference for computational imaging, which combines vision, graphics, signal processing, and optics, http://compimagingbook.com
* *CVPR 2021 Tutorial on Physics-Based Differentiable Rendering*, June 20, 2021,  https://diff-render.org
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling the Plenoptic Function
* Data-Driven Computational Imaging http://ciml.media.mit.edu
* https://cseweb.ucsd.edu/~viscomp/projects/LF/
* https://dellaert.github.io/NeRF
 ### Visual Sensing Using Machine Learning
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling Plenoptic Function
* https://github.com/tensorflow/graphics
* * Neural Radiance Fields (NeRF) turns 2D pictures into navigable models:
* https://www.techeblog.com/google-nerf-in-the-wild-2d-photo-3d-model/
