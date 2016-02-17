[title]: - "Image Analysis of Human Brain - Freesurfer Workflow on OSG"
[TOC]
 
## Overview

[FreeSurfer](http://freesurfer.net/) is a software package to analyze MRI images of human brain subjects. The OSG has developed a command line utility, `fsurf`, that simplifies FreeSurfer computation on the Open Science Grid. The advantages of using  `fsurf` are:

     (1) Handles job submission to OSG using appropriate flags for multi-core job slots
     (2) Handles data transfer to and from remote worker nodes
     (3) Provides a complete pipeline to analyze an MRI image 

![fig 1](https://raw.githubusercontent.com/OSGConnect/tutorial-FreeSurfer/master/Figs/freesurfer_image_from_net.png )

Important note:  The fsurf tool is  not HIPPA compliant. Images must be anonymized and deidentified before submission.  See the 
section on "How to ananymize and deidentify images". 

