# Sehender Raum / Seeing Space
Catching up with newer research in image based rendering: A TLDR on how traditional computer graphics will change with neural rendering. And how it fits with computer vision, machine learning and capture hardware.

With neural rendering computer graphics and vision might be heading for a moment where we can see and remove some limiting assumptions about what it is about. Some disjoint pieces may just fall into place: Computer graphics and vision now have a shared framework rather than being in nther own little boxes. We got things wrong and the possibilitis are exciting.
I spent enternity on tools for generating photoreal 3d environments, now its easy as snap chat. We can have realtime google earth. I am allowed to be damned excited.

* TLDR: <a href="https://docs.google.com/presentation/d/1eXI6p4hGbzW6OoX3S7gc-nZbHLf5bjqtqY36pFTXLOc/edit?usp=sharing">Nerf in 10 slides</a>
 
Moved to the <a href="https://github.com/3a1b2c3/seeingSpace/wiki">WIKI</a> of this repo.

Overview and papers:

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/1)-Sehender-Raum-:-Seeing-Space">1) Understanding Neural rendering (START HERE)</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/2)-Nerf-(radiance-fields)-basics,-frameworks-and-real-world-uses">2) Nerf (radiance fields) basics, frameworks and real world uses</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/3)-Nerf-for-3d-mapping:-aka-Google-live-view-and-Apple-Fly-around">3) Nerf for 3d mapping: aka Google live view and Apple Fly around</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/4)-Nerf-Editing:-Relighting,-mesh-extraction-and-scene-segmentation"> 4 )Nerf-for-relighting,-mesh-extraction-and-scene-segmentation">4) Nerf for relighting, mesh extraction, scene segmentation</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/5)-Nerf-Editing:-Relighting,-mesh-extraction-and-scene-segmentation"> 5 )Nerf-for-relighting,-mesh-extraction-and-scene-segmentation">4) Nerf for relighting, mesh extraction, scene segmentation</a>

<a href="https://github.com/3a1b2c3/seeingSpace/wiki/6)-Related-fields-(Photogrametry,-LIDAR,-SLAM-etc)">6) Related fields (Photogrametry, LIDAR, SLAM etc)</a>

Hands on:
* <a href="https://github.com/3a1b2c3/seeingSpace/wiki/Code-Experiments:-Nerf-frameworks">Hands on: Nerf-frameworks</a>
* <a href="https://github.com/3a1b2c3/seeingSpace/wiki/Hands-on:-Representing-plants-as-Nerf">Hands on: Representing plants as Nerf</a>


Why does it matter? It might be the biggest change change to graphics since the mid 80ies: We can now **render lightfields**, the holy grail of graphics. We know about light fields since <a href="https://github.com/3a1b2c3/seeingSpace/wiki/1)-Sehender-Raum-:-Seeing-Space#the-plenoptic-function-8d--gabriel-lippmann--1908">**1908**</a> but until 3 years ago they were just too expensive and impractical to create and render ("flying cars", possible but why do it?). You needed the camera on the picture below at least, now you render one from **30 frames of compressed video** from youtube and a gaming machine or the cloud...
<img src="https://user-images.githubusercontent.com/74843139/173516914-cc56b3d4-8fff-49d3-968a-2ea47e259de9.png" width=450>




Getting hands on: Google colab setup for **NVIDIA Instant Nerf**, 
so you can  try before buying a new gpu <a href="https://github.com/3a1b2c3/seeingSpace/wiki/NVIDIA-instant-Nerf-on-google-colab,-train-a-nerf-without-a-massive-gpu">Step by step</a>


<img src="https://user-images.githubusercontent.com/74843139/172032036-f33a63f2-6a88-4ae5-b8df-fbbb72463592.png" width=450>

