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

Adding test lines from NAMD 

## NAMD tutorial files


It is easiest to start with the `tutorial` command. In the command prompt, type
	 $ tutorial namd # Copies input and script files to the directory tutorial-namd.
 
This will create a directory `tutorial-namd`. Inside the directory, you will see the following files

	namd_run.submit            # Condor job submission script file.
	namd_run.sh                # Job execution script file.
	ubq_gbis_eq.conf           # Input configuration for NAMD.
	ubq.pdb                    # Input pdb file for NAMD.
	ubq.psf                    # Input file for NAMD.
	par_all27_prot_lipid.inp   # Parameter file for NAMD.


Here, `namd_run.submit` and `namd_run.sh` are the script files related to job submission and the other files are required by the NAMD software. The details regarding the preparation of input files for NAMD is available external website for NAMD at UIUC. 




