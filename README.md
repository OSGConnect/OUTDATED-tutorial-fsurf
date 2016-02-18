[title]: - "Image Analysis of Human Brain - FreeSurfer Workflow on OSG"
[TOC]
 
## Overview

[FreeSurfer](http://freesurfer.net/) is a software package to analyze MRI images of human brain subjects. The OSG has developed a command line utility, `fsurf`, that simplifies `FreeSurfer` computation on the Open Science Grid (OSG). The advantages of using  `fsurf` are:

     (1) Handles job submission to OSG using appropriate flags for multi-core job slots
     (2) Handles data transfer to and from remote worker nodes
     (3) Provides a complete pipeline to analyze an MRI image 


![fig 1](https://raw.githubusercontent.com/OSGConnect/tutorial-FreeSurfer/master/Figs/freesurfer_image_from_net.png )

Important note on Data privacy: The fsurf tool is not HIPPA compliant. Images must be anonymized and deidentified before submission.  See the 
section `How to anonymize images` at the end of this tutorial.  


##  Initial Setup 

You could set up and use the `fsurf` workflow on your laptop (desktop) or on the OSG Connect login node. Weather you run `fsurf` from 
laptop or from OSG Connect submit node, your actual `FreeSurfer` jobs are executed on remote OSG machines.

###  Setup on OSG Connect submit node

First, ssh to the OSG Connect's login node

     $ ssh username@login.osgconnect.net

You need to set up the `fsurf` tool. Run on the command line:

     $ fsurf-config

This command may be placed at the end of your .bashrc file so that it will automatically run when you log in.


###  Setup on laptop or desktop

Set up `fsurf` on your laptop or desktop (linux/unix/MacOS X OS system) as a remote client with the `curl` command which is a utility to copy the content of the url. Open a terminal window and then run:

      curl -L -o fsurf 'http://stash.osgconnect.net/+fsurf/fsurf'
      chmod +x fsurf 

While using the local laptop or desktop version of fsurf, you  specify `username` using the `--user` argument and the `password` 
using `--password`.  For example,  

      fsurf --submit --subject test --user myuser --password mypassword

the argument `myuser` is your username and `mypassword` is your password for your OSG Connect account. 

###  Check the fsurf setup and options

To check the initial set up of `fsurf` on OSG Connect or your laptop (desktop), invoke the help argument. 

      $ fsurf --help

If you see the following message, the setup of `fsurf` is fine. 

      usage: fsurf [-h] [--version] [--submit] [--list] [--status] [--remove]
                   [--output] [--log] [--id WORKFLOW_ID] [--subject SUBJECT]
                   [--dir INPUT_DIRECTORY] [--dualcore] [--verbose]

      Process freesurfer information

      optional arguments:
        -h, --help            show this help message and exit
        --version             show program's version number and exit
        --submit              Submit job for processing
        --list                List current jobs
        --status              Get status of a specified job
        --remove              Remove specified job
        --output              Get output from specified job
        --log                 Get log file for specified job
        --id WORKFLOW_ID      id that specifies job
        --subject SUBJECT     Subject id to process
        --dir INPUT_DIRECTORY
                        directory containing input file
        --dualcore            Use 2 cores to process certain steps
        --verbose             Enable verbose output for status action


##  Process a Scan

A typical image analysis requires doing calculations on multiple-stages via autorecon1, autorecon2, and autorecon3.  All three steps are conveniently handled by `fsurf`. 

Get the sample MRI file by 

     curl -L -o fsurf 'http://stash.osgconnect.net/+fsurf/test_defaced.mgz'

the file `test_defaced.mgz` is the defaced sample file. 

Now we do an analysis on `test_defaced.mgz`. In the file `test_defaced.mgz` the prefix `test` is the name of the subject which could be a number or name while the format  is `mgz` file format.


     $ fsurf  --submit --subject test --dir $PWD

The `FreeSurfer` requires that the MRI file to be deidentified and defaced. The supplied `test_defaced.mgz` is already deidentified and defaced, so say `y` to the following questions. 

     Has the MRI data been deidentified (This is required) [y/n]? y
     Has the MRI data been defaced (This is recommended) [y/n]? y

After typing `y` to the above two questions, `fsurf` creates and submits the workflow 

     Creating and submitting workflow
     Workflow submitted with an id of 20160119T100055-0600

The id of your workflow is `20160119T100055-0600`. The id is useful for checking, removing and getting the output of the workflow. 


###  List Workflows

Run the command below to get a list of ids for your workflows that are running or have already  completed:

     $ fsurf --list 
     Current workflows
     Subject    Workflow             Submit time          Cores Used      Status    
     test       20160119T100055-0600 10:00 01-19-2016     2               Running   


###  Get output from a completed workflow

See the workflow is completed. In the event of completion, the staus of the workflow should be `Success` as follows

     $ fsurf --list 
     Current workflows
     Subject    Workflow             Submit time          Cores Used      Status    
     test       20160119T100055-0600 10:00 01-19-2016     2               Success   

Run the command below to get the output of the completed workflow `20160119T100055-0600`:
 
     $ fsurf --output --id 20160119T100055-0600

Depending on the computer resources available this will typically require several hours to complete.  The output will be saved as an archive in the current working directory: 20160119T100055-0600.tar.bz2 . You can extract all the files in the archive using: 

    $ tar -jxvf 20160119T100055-0600.tar.bz2
 
 Similarly, you get the output of any completed  workflow with id `WorkflowID` 
 
     $ fsurf --output -id WorkflowID
     $ tar -jxvf WorkflowID.tar.bz2

###  Remove a running or completed workflow

Run on the command line to remove an existing workflow:
   
    $ fsurf --remove --id WorkflowID

For example, to remove a running worflow with an id `20160119T100055-0600`, type

    $ fsurf --remove --id 20160119T100055-0600
    Workflow 20160119T100055-0600 removed successfully
    Waiting for running jobs to be removed...
    Jobs removed, removing workflow directory

This will not effect the files you have fetched with fsurf --output --id WorkflowID.

##  How to anonymize images 

Since `fsurf` is not HIPPA compliant, the scan file should be deidentified and defaced on your local machine.  You can use your local `FreeSurfer`
installation to prepare your scans. The original DICON scan produce several slices. First, combine the slices and create single image file by

      $ recon-all -subject SUBJECT -i PATH_TO_ONE_OF_THE_SLICES

Here, `recon-all` is the `FreeSurfer` command line tool, the argument `SUBJECT` is the name of the subject, and the argument `PATH_TO_ONE_OF_THE_SLICES` is the name of just one DICON file with full path information. The above command produces a single compressed image file `001.mgz`
under the directory `subjects/SUBJECT/mri/orig`. Now deface the image `001.mgz` to to `SUBJECT_defaced.mgz` with the `mri_deface` command as follows,

      $ mri_deface ../subjects/SUBJECT/mri/orig/001.mgz  talairach_mixed_with_skull.gca  face.gca  ../subjects/SUBJECT/mri/orig/SUBJECT_defaced.mgz



If the `mri_deface` program cannot find the needed `*.gca` files (the standard FreeSurfer parameter files), fetch and unzip them:

     $ wget "http://stash.osgconnect.net/@freesurfer/face.gca"
     $ wget "http://stash.osgconnect.net/@freesurfer/talairach_mixed_with_skull.gca"

## Getting Help 
For assistance or questions, please email the OSG User Support team  at [user-support@opensciencegrid.org](mailto:user-support@opensciencegrid.org) or visit the [help desk and community forums](http://support.opensciencegrid.org).



