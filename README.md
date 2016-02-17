[title]: - "Image Analysis of Human Brain - Freesurfer Workflow on OSG"
[TOC]
 

Freesurfer is software package to analyze the MRI images of human brains. Here, we introdue the `fsurf` workflow that utlizes FreeSurfer.  `fsurf` is a complete pipeline for the brain image analysis on distributed high throughput 
computing platform. The advantages of using  `fsurf` are

     (1) No need to worry about the job schedular and write a job description file
     (2) Provides a complete pipeline to analyze the MRI image 
     (3) No need to worry about how to do the data transfers from/to the remote worker machines
     (4) Perfom analysis on multiple subjects via distributed high throughput computing

