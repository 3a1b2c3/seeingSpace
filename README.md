#  Sehender Raum / Seeing Space
## Notes about capturing, rendering and digitally reconstruction the world
When I learned about (traditional) computer graphics I think i missed the big picture about how all the pieces connect with hardware, physics and machine learning aspects. 
It makes it harder to understand recent research and its meaning for the field.
A minimal framework how to think about graphics and how "traditional approach" fit with newer research like Nerfs.

## Beyond traditional computer graphics and capture technology: Data-Driven Computational Imaging

#### Image-based rendering: Plenoptic function and cameras, light fields
#### Plenoptic function
The rendering equation describes light transport for a single camera or the human eyes
<img src="https://user-images.githubusercontent.com/74843139/134788604-b920d1c9-bb65-408a-9eb1-eab3ea1d4408.png" width=300>
<img src="https://user-images.githubusercontent.com/74843139/134788646-69861df7-4656-449e-adb4-210c8e0307ff.png" width=300>

Inspired by insect eyes or camera arrays the plenoptic function generalizes this idea:
<img src="https://user-images.githubusercontent.com/74843139/134788591-e65ba01b-3dda-407b-9f91-712af9f224e8.png" width=300>
#### View interpolation 
### 3d scene reconstruction and inverse rendering
#### Visual Sensing Using Machine Learning
##### Neural Radiance Fields (NeRF) 


## Recommended reading
 
### Data-Driven Computational Imaging
* *Computational Imaging* By Ayush Bhandari, Achuta Kadambi and Ramesh Raskar, A comprehensive and up-to-date textbook and reference for computational imaging, which combines vision, graphics, signal processing, and optics, http://compimagingbook.com/
* *CVPR 2021 Tutorial on Physics-Based Differentiable Rendering*, June 20, 2021,  https://diff-render.org
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling the Plenoptic Function
* Data-Driven Computational Imaging http://ciml.media.mit.edu
* Neural Radiance Fields (NeRF) turns 2D pictures into navigable models:
* https://www.techeblog.com/google-nerf-in-the-wild-2d-photo-3d-model/
* https://cseweb.ucsd.edu/~viscomp/projects/LF/
* https://dellaert.github.io/NeRF
* https://arxiv.org/pdf/2007.15194.pdf Crowdsampling Plenoptic Function
