---
title: Group work`:` how to parallelize a problem?
author: CSC training
date:   2021
lang:   en
---



# Smoothed Particle Hydrodynamic simulation

<div class=column>
* Particles are hard spheres
* They interact with neighbours inside some effective range
	- This way the particles appear “viscous”
* See live demo 

</div>
<div class=column>

 ![](img/smooth_particle.svg){.center width=80%}

</div>

# Group work assignment

* Within your breakout room, discuss the Smoothed Particle Hydrodynamic (SPH) problem
* Make back-of-an-envelope sketches, etc, on how would you parallelize the selected problem
	- Which parts of the work can be carried out independently and in parallel?
	- What kind of coordination between the parallel tasks will be needed?
* If you come up with several approaches, discuss their pros and cons

