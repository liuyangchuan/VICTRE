# VICTRE
![](https://user-images.githubusercontent.com/5750606/41682198-0b250648-74a5-11e8-9578-f93602efa5ab.png)\
***Virtual Imaging Clinical Trial for Regulatory Evaluation***

Clinical trails are expensive and delay the regulatory evaluation and early patient access to novel devices. In order to demonstrate an alternative approach, a recent effort at the Division of Imaging, Diagnostics, and Software Reliability at the U.S. Food and Drug Administration (known as the VICTRE project) demonstrated the replication of one such clinical trial using completely in-silico tools and compared results in terms of imaging modality performance between the human trial and the computational trial. The VICTRE trial consisted in imaging 3000 digital breast models in digital mammography and digital breast tomosynthesis system models. On this page we are making all the in silico components of VICTRE freely available to the community, and also sharing the entire VICTRE pipeline as a container which can be run either on locally or in the cloud.

*VICTRE team: Aldo Badano, Ph. D., Christian G. Graff, Ph. D., Andreu Badal, Ph. D., Diksha Sharma, M. Sc., Rongping Zeng, Ph. D., Frank W. Samuelson, Ph. D., Stephen Glick, Ph. D., and Kyle J. Myers, Ph. D.*

Disclaimer
----------

This software and documentation (the "Software") were developed at the Food and Drug Administration (FDA) by employees of the Federal Government in the course of their official duties. Pursuant to Title 17, Section 105 of the United States Code, this work is not subject to copyright protection and is in the public domain. Permission is hereby granted, free of charge, to any person obtaining a copy of the Software, to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, or sell copies of the Software or derivatives, and to permit persons to whom the Software is furnished to do so. FDA assumes no responsibility whatsoever for use by other
parties of the Software, its source code, documentation or compiled executables, and makes no guarantees, expressed or implied, about its quality, reliability, or any other characteristic. Further, use of this code in no way implies endorsement by the FDA or confers any advantage in regulatory decisions. Although this software can be redistributed and/or modified freely, we ask that any derivative works bear some notice that they are derived from it, and any modified versions bear some notice that they have been modified. 


The VICTRE pipeline
-------------------

VICTRE replicates various steps in silico of the comparative clinical trial. The pipeline consists of 7 main parts:

* **Phantom generation** \
Breast models (in silico subjects) are generated using a procedural analytic model.  The model allows for varying patient characteristics including breast shape, glandularity and density, and size.  Details on downloading and running this module can be found at [phantom generation code](https://github.com/DIDSR).  This code is written in C++.
  
* **Phantom compression and cropping** \
Breast models are then compressed using the open-source finite-element software FeBio and cropped to a fixed volume to speed up loading and fit them in the limited Graphics Processing Units (GPU) memory.  This module can be downloaded from [phantom compression and cropping code](https://github.com/DIDSR).  These codes are written in C++.

* **Lesion insertion** \
Lesions are then inserted in a subset of the compressed breast phantom population to create cancer cases.  The lesion insertion locations are randomly chosen from the list of possible locations given by the breast phantom generation code.  The selected location is then passed through checks to ensure that the lesion is within the phantom boundaries, non-overlapping with tissues like air/muscle/nipple/skin, and non-overlapping with already inserted lesions.  This code is available under [Lesion Insertion](https://github.com/DIDSR/VICTRE/tree/master/Lesion%20Insertion).  This code is developed using Python.

* **X-ray Imaging** \
These in-silico patients are then imaged using a state-of-the-art Monte Carlo x-ray transport code (MC-GPU).  We obtained projection images for the two modalities in the VICTRE trial: full-field digital mammography (DM) and digital breast tomosynthesis (DBT).  Details on downloading and running this module can be found at [MC-GPU code](https://github.com/DIDSR).  This code is developed using C and CUDA.

* **Reconstruction** \
VICTRE implemented an filtered back-projection (FBP) reconstruction algorithm for DBT using single-threaded C based on Fessler's cone beam computed tomography (CBCT) reconstruction toolbox.  We modified an extension of the single-threaded C code developed by Leeser \emph{et al.} for reconstruction of CBCT projections to allow for DBT reconstruction.  The modifications account for an x-ray source moving in an arc about the object with a stationary detector with the z-axis of the object normal to the detector plane.  Code and instructions available under [FBP DBT reconstruction in C](https://github.com/DIDSR/VICTRE/tree/master/FBP%20DBT%20reconstruction%20in%20C).  We also have the same code available in Matlab, available at [reconstruction code using Matlab](https://github.com/DIDSR/ReconDBT).

* **Regions/Volumes of interest (ROI/VOIs) extraction** \
After the images are acquired, lesion-present and lesion-absent ROIs are extracted from the DM images (or VOIs from the DBT volumes).  For extracting lesion-absent ROIs, we applied rigorous checks including if the subimages are within the reconstructed volume boundaries and non-overlapping, to find appropriate locations.  This code is available under [ROI Extraction](https://github.com/DIDSR/VICTRE/tree/master/ROI%20Extraction).  This code is developed using Python.

* **Reader models** \
The ROIs are then interpreted by in silico readers using a location-known exactly paradigm.  Code and instructions of use can be downloaded from [reader models](https://github.com/DIDSR).  This code is written in Matlab.


The VICTRE container
--------------------

Although operating systems (OS) and software platforms have evolved throughout the years, application sharing remains a challenge in deploying across many systems. One of the emerging solution to this issue is to use *containers* - a technology which allows the user to package and isolate a set of processes (applications) with their entire run-time environment.

Since each part of the VICTRE pipeline has its own codes and related dependencies, it is challenging to integrate them together in one package that would run on a variety of servers. For this we plan to make use of Docker containers by including all required dependencies under one environment for easy installation and deployment. Docker provides operating system level virtualization on Linux and Windows platforms. The environment for the docker containers is defined by a Dockerfile which allows different servers to install the same set of libraries and dependencies as needed. We will make the VICTRE container available soon.


VICTRE image datasets
---------------------

VICTRE image datasets in DICOM format will soon be available for [download](https://wiki.cancerimagingarchive.net/display/Public/VICTRE) at the Cancer Imaging Archive.  VICTRE images are converted from Raw to DICOM. Metadata such as the image generation are added as the DICOM tags to allow for reproducibility. DICOM tags include patient information, clinical trial description, imaging study performed per modality and series under each study, and breast type, lesion absence or presence, and compressed breast thickness are included as attributes of the patient.
