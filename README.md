[title]: - "FreeSurfer"
[TOC]
 
## Overview

[FreeSurfer](http://freesurfer.net/) is software package to analyze the MRI images of human brains. Here, we introdue you to 
the `fsurf` workflow that utlizes freesurfer.  `fsurf` is a complete pipe-line for the brain image analysis on distributed high throughput 
computing platform. The advantages of using  `fsurf` are

(1) No need to worry about the job schedular ( on OSG, no need of writing the condor job description files)
(2) Provides a complete pipe-line to analyze the MRI image 
(3) No need to worry about the data transfers
(4) Perfom analysis on multiple subjects via distributed high throughput computing

![fig 1](https://raw.githubusercontent.com/OSGConnect/tutorial-fsurfer/master/Figs/freesurfer_image_from_net.png )

## Tutorial files

It is easiest to start with the `tutorial` command. In the command prompt, type

     $ tutorial fsurf # Copies input image file 

This will create a directory `tutorial-fsurf`. Inside the directory, you will see the following files

    test_defaced.mgz              # Input brain MRI file

Here, `test_defaced.mgz` is the brain MRI file. 


## Initial configuration

You need to set up the `fsurf` tool. Run on the command line:

     $ fsurf-config

This command may be placed at the end of your .bashrc file so that it will automatically run when you log in. 

## Process a Scan

Now we will process a scan of `test_defaced.mgz`.  A typical image analysis requires multiple-stages via  autorecon1, autorecon2, 
and autorecon3.  All the above three steps are efficiently performed by `fsurf` as 

     $ fsurf  --submit --subject test --dir $PWD


### List Workflows

Run the command below to get a list of ids for your workflows that  are running or have already  completed:

    $ fsurf --list 


      Current workflows
      Subject	Workflow         	Submit time      	Cores Used  	Status
      test    	20160104T112401-0600    11:24 01-04-2016 	2           	Running

### Get output from a completed workflow

Run the command below to get the output from a workflow that’s completed:
 
     $ fsurf --output --id WORKFLOW

Depending on the computer resources available this will typically require several hours to complete.  The output will be saved as an archive in the current working directory: WORKFLOW.tar.bz2 . You can extract all the files in the archive using: 

    $ tar -jxvf WORKFLOW.tar.bz2
  
    $ fsurf --output --id 20160104T112401-0600
    $ tar -jxvf 20160104T112401-0600.tar.bz2

### Remove a running or completed workflow

Run on the command line to remove an existing workflow:
   
    $ fsurf --remove --id WORKFLOW
  
    $ fsurf --remove --id 20160104T112401-0600
    Workflow 20160104T112401-0600 removed successfully
    Waiting for running jobs to be removed...
    Jobs removed, removing workflow directory

This will not effect the files you have fetched with fsurf --output --id WORKFLOW.

## Getting Help
For assistance or questions, please email the OSG User Support team  at [user-support@opensciencegrid.org](mailto:user-support@opensciencegrid.org) or visit the [help desk and community forums](http://support.opensciencegrid.org).
