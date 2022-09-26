# Sehender Raum / Seeing Space
Catching up with newer research in image based rendering: A TLDR on how traditional computer graphics will change with neural rendering. And how it fits with computer vision, machine learning and capture hardware.

With neural rendering computer graphics and vision might be heading for its "Charles Darwin" moment where we can see and remove some limiting assumptions about what it is about. Some disjoint pieces may just fall into place: Computer graphics and vision now have a shared framework.

* TLDR: <a href="https://docs.google.com/presentation/d/1eXI6p4hGbzW6OoX3S7gc-nZbHLf5bjqtqY36pFTXLOc/edit?usp=sharing">Nerf in 10 slides</a>
 
Moved to the <a href="https://github.com/3a1b2c3/seeingSpace/wiki">WIKI</a> of this repo.

Overview and papers:
<a href="https://github.com/3a1b2c3/seeingSpace/wiki/1)-Sehender-Raum-:-Seeing-Space">1) Understanding Neural rendering (START HERE)</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/2)-Nerf-(radiance-fields)-basics-and-encodings">2) Nerf (radiance fields) basics and encodings</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/3)-Nerf-for-3d-mapping:-aka-Google-live-view-and-Apple-Fly-around">3) Nerf for 3d mapping: aka Google live view and Apple Fly around</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/4)-Nerf-for-relighting,-mesh-extraction,-scene-segmentation)">4) Nerf for relighting, mesh extraction, scene segmentation</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/5)-Related-fields-(Photogrametry,-LIDAR,-SLAM-etc)">5) Related fields (Photogrametry, LIDAR, SLAM etc)</a>

Hands on:
* <a href="https://github.com/3a1b2c3/seeingSpace/wiki/Instant-ngp,-nvdiffrec-and-Kaolin-wisp-library">NVIDIA research libraries and papers</a>
* <a href="https://github.com/3a1b2c3/seeingSpace/wiki/Hands-on:-Representing-plants-as-Nerf">Hands on: Representing plants as Nerf</a>


Why does it matter? It might be the biggest change change to graphics since the mid 80ies: We can now **render lightfields**, the holy grail of graphics. We know about light fields since **1936** but until 2.5 years ago they were just too expensive and impractical to create and render. You needed the camera on the picture below at least, now you render one from **30 frames of compressed video** from youtube and a gaming machine or the cloud...
![image](https://user-images.githubusercontent.com/74843139/173516914-cc56b3d4-8fff-49d3-968a-2ea47e259de9.png)




Getting hands on: Google colab setup for **NVIDIA Instant Nerf**, 
so you can  try before buying a new gpu <a href="https://github.com/3a1b2c3/seeingSpace/wiki/NVIDIA-instant-Nerf-on-google-colab,-train-a-nerf-without-a-massive-gpu">Step by step</a>


<img src="https://user-images.githubusercontent.com/74843139/172032036-f33a63f2-6a88-4ae5-b8df-fbbb72463592.png" width=450>

