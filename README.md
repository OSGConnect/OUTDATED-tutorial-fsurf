[title]: - "Image Analysis of Human Brain - Freesurfer Workflow on OSG"
[TOC]
 
## Overview

[FreeSurfer](http://freesurfer.net/) is a software package to analyze MRI images of human brains. The OSG has developed a command line utility, `fsurf`, that simplifies FreeSurfer computation on the Open Science Grid. The advantages of using  `fsurf` are:

     (1) No need to worry about the job scheduler and write a HTCondor job description file
     (2) Provides a complete pipeline to analyze an MRI image 
     (3) No need to worry about tranferring data to remote worker machines
     (4) Perfom analysis on multiple subjects via distributed high-throughput computing

![fig 1](https://raw.githubusercontent.com/OSGConnect/tutorial-FreeSurfer/master/Figs/freesurfer_image_from_net.png )

