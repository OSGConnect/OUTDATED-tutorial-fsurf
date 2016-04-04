[title]: - "Image Analysis of Human Brain - FreeSurfer Workflow on OSG"
[TOC]
 
## Overview

[FreeSurfer](http://freesurfer.net/) is a software package to analyze MRI images of human brain subjects. The OSG has developed a command line utility, `fsurf`, that simplifies `FreeSurfer` computation on the Open Science Grid (OSG).  Among its features:

* Handles job submission to OSG using appropriate flags for multi-core job slots
* Transfers image data to and from remote worker nodes
* Provides a complete pipeline to analyze an MRI image 

In this tutorial, we first describe the initial set up of `fsurf` on local machine or login node. Next, we will learn the usage of  `fsurf` to run image analysis on OSG and get the output files. 

![fig 1](https://raw.githubusercontent.com/OSGConnect/tutorial-FreeSurfer/master/Figs/freesurfer_image_from_net.png )

**Important note on data privacy**: The `fsurf` tool is not HIPPA compliant (Health Insurance Portability and Accountability Act (HIPPA) is federal law to protect medical information). Therefore images must be anonymized and deidentified before they are uploaded to OSG servers.  See the section `Anonymize Images` below for more details.  

##  Anonymize Images 

Since OSG resources are not HIPPA compliant, the MRI images must be deidentified and defaced on your local machine before being used.  You can use a local `FreeSurfer` installation to prepare your scans. First, import your image by running

      $ recon-all -subject SUBJECT -i PATH_TO_MGZ_INPUT_FILE

Here, `recon-all` is the `FreeSurfer` command line tool, the argument `SUBJECT` is the name of the subject, and the argument `PATH_TO_MGZ_INPUT_FILE` is the  full path to the input file. The above command produces a single compressed image file `001.mgz`
under the directory `subjects/SUBJECT/mri/orig`. Now deface the image `001.mgz` to to `SUBJECT_defaced.mgz` with the `mri_deface` command as follows,

      $ cd  ${FREESURFER_HOME}/average
      $ mri_deface ../subjects/SUBJECT/mri/orig/001.mgz  \
                   talairach_mixed_with_skull.gca  face.gca \
                   ${FREESURFER_HOME}/subjects/SUBJECT/mri/orig/SUBJECT_defaced.mgz

If the `mri_deface` program cannot find the needed `*.gca` files (the standard FreeSurfer parameter files), fetch and unzip them:

     $ cd ${FREESURFER_HOME}/average
     $ wget "http://stash.osgconnect.net/@freesurfer/face.gca"
     $ wget "http://stash.osgconnect.net/@freesurfer/talairach_mixed_with_skull.gca"


##  Initial Setup 

You could set up and use the `fsurf` workflow on a local machine (your laptop/desktop/lab machine) or on the OSG Connect login node. Whether  you run `fsurf` from a local machine or the login node, your actual `FreeSurfer` jobs are executed on remote OSG machines.

###  Login Node

The tool `fsurf` is already installed on the OSG Connect login node. You just need to configure it in order to utilize `fsurf` on 
login node. First, ssh to the OSG Connect's login node

     $ ssh username@login.osgconnect.net

and run

     $ fsurf-config

You should only need to run this command once in order to setup the fsurf configuration for you.


###  Local Machine 

Set up `fsurf` on your laptop or desktop (linux/unix/MacOS X OS system) as a remote client with the `curl` which downloads files hosted on web servers. Open a terminal window and then run:

      curl -L -o fsurf 'http://stash.osgconnect.net/+fsurf/fsurf'
      chmod +x fsurf 

While using the local laptop or desktop version of fsurf, you  specify `username` using the `--user` argument and the `password` 
using `--password`.  For example,  

      fsurf --submit --subject test --user myuser --password mypassword

the argument `myuser` is your username and `mypassword` is your password for your fsurf account.  If you don't have a fsurf password, open a ticket requesting an account [https://support.opensciencegrid.org/support/tickets/new](here). 

###  Check Setup

To check `fsurf` setup and the available options, running 

      $ fsurf --help

will print the following message. 

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

If you see the above message on the terminal, the setup of `fsurf` is fine and you are ready to analyze MRI images. 

##  Process a Scan

A typical image analysis requires doing calculations on multiple-stages via autorecon1, autorecon2, and autorecon3.  All three steps are conveniently handled by `fsurf`. 

Get a sample MRI file by running

     curl -L -o fsurf 'http://stash.osgconnect.net/+fsurf/test_defaced.mgz'

the file `test_defaced.mgz` is the defaced sample file. 

Now we do an analysis on `test_defaced.mgz`. In the file `test_defaced.mgz` the prefix `test` is the name of the subject.


     $ fsurf  --submit --subject test --dir $PWD

The `FreeSurfer` requires that the MRI file to be deidentified and defaced. The supplied `test_defaced.mgz` is already deidentified and defaced, so say `y` to the following questions. 

     Has the MRI data been deidentified (This is required) [y/n]? y
     Has the MRI data been defaced (This is recommended) [y/n]? y

After typing `y` to the above two questions, `fsurf` creates and submits the workflow 

     Creating and submitting workflow
     Workflow submitted with an id of 20160119T100055-0600

The id of your workflow is `20160119T100055-0600`. The id is needed to check the status, remove and get the output of the workflow. 


###  List Workflows

Run the command below to get a list of workflows that you have submitted and their status:

     $ fsurf --list 
     Current workflows
     Subject    Workflow             Submit time          Cores Used      Status    
     test       20160119T100055-0600 10:00 01-19-2016     2               Running   


###  Get Outputs

Once a workflow is completed successfully, the status of the workflow should be `COMPLETED` as can be seen below

     $ fsurf --list 
     Current workflows
     Subject    Workflow             Submit time          Cores Used      Status    
     test       20160119T100055-0600 10:00 01-19-2016     2               COMPLETED   

Run the command below to get the output of the completed workflow `20160119T100055-0600`:
 
     $ fsurf --output --id 20160119T100055-0600

Depending on the computer resources available, a workflow will typically require 6-12 hours to complete.  The output will be saved as an archive in the current working directory: 20160119T100055-0600.tar.bz2 . You can extract all the files in the archive using: 

    $ tar -jxvf 20160119T100055-0600.tar.bz2
 
 Similarly, you get the output of any completed  workflow with id `WorkflowID` 
 
     $ fsurf --output -id WorkflowID
     $ tar -jxvf WorkflowID.tar.bz2

###  Remove Workflows

Run the following to remove an existing workflow:
   
    $ fsurf --remove --id WorkflowID

For example, to remove a running worflow with an id `20160119T100055-0600`, type

    $ fsurf --remove --id 20160119T100055-0600
    Workflow 20160119T100055-0600 removed successfully
    Waiting for running jobs to be removed...
    Jobs removed, removing workflow directory

This will not effect the files you have fetched previously.


## Getting Help 
For assistance or questions, please email the OSG User Support team  at [user-support@opensciencegrid.org](mailto:user-support@opensciencegrid.org) or visit the [help desk and community forums](http://support.opensciencegrid.org).



