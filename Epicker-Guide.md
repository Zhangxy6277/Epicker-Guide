# EPicker Guide

---
>Last editted by Tianfang, 2021.4.12

## Overview

### What is EPicker?

EPicker is a standalone software for particle picking in cryo-EM. It is implemented based on CenterNet, an open-source deep learning detection framework. Trained and well tested on datasets containing heterogenous particles, EPicker is able to pick particles, vesicles and filaments effectively. Different from other particle picking tools, EPicker builds up a continual learning mechanism. A previously unseen type of particles can be well learnt after trained with a small dataset of old particles, yielding a new model able to recognize both new particles and old ones. With this advantage, you can continually upgrade your model and save a lot of storage for model saving and data saving used for traditional retraining procedures.

### About this guide

This user guide includes all instructions you may need for installation, picking and training. A tutorial is also provided at the end of this guide to help you walk through the complete workflow of EPicker. If you have any questions or if anything does not work, please contact me: ***zhaotianfang2010@163.com***

## Installation

### Requirements

#### Hardware

A GPU is essential to run a deep learning program. We have tested EPicker on following GPUs.  

- NVIDIA RTX 2080Ti  
- NVIDIA Tesla P100  
- NVIDIA Tesla A100  
- NVIDIA Titan X 

#### System

EPicker is able to run on following systems

- CentOS 7
- Ubuntu 18.04
- Ubuntu 16.04

A version for Windows is not available, but it's coming soon.

#### GCC and CUDA

The deep learning programming framework we use is pytorch. And because pytorch does not have a version that is compatible all CUDA versions, we provide installation packages for different environments. For an old version EPicker, CUDA9.0 and G++ 4.8.5 are enough to conduct installation. For higher versions, G++>=5.0 and CUDA10+ are needed.

#### Network

Considering some GPU nodes may not have access to Internet, we additionally provide offline packages. All third party dependencies are included in this installation script. You only need to download an appropriate package and simply run it in your terminal.

Certainly, if your machine is Internet-available, you also need to download an installation script. But third party dependencies will be downloaded through the installer according to your local envrionment.

### Download

Please check your environment before downloading and installation.  
>(20210412: Package names below should be replaced with downloading links. Tianfang)

For online installation  

- [Epicker-install.sh]()  

For offline installation  

- [Epicker-cu101.sh]()  
- [Epicker-cu102.sh]()  
- [Epicker-cu110.sh]()  

### Script Installation

For both ways of installation, the only thing you need to do is to run a linux script like this.
>$cd {Path to EPicker}  

>$./EPicker-cu110.sh  

If your default g++ version is below 5.0, you can simply specify a g++ path for compiling.
>$./EPicker-cu110.sh {Path to g++}

## Picking

### Command

Picking script is in Epicker/Epicker/bin/
>$cd Epicker/Epicker/bin/  

>$./epicker.sh {Parameters}
###Essential Parameters
Set your input and ouput path, select your model and switch to a working mode with these parameters.

- **data** Micrographs to be picked, which can be a mrc file, a path to mrc files or a thi file. For information of THI file, please refer to [THI file](#thi).
- **load_model** Pretrained model. Three general models for picking particles, liposomes and filaments are available at [EPicker models]().
- **mode** (default=particle) Picking mode: particle, vesicle or fiber. 
- **output** Folder of output coordinate files.

### Optional parameters

These parameters are also useful and may influence your final picking results. We recommend you changing them to fit your own picking job, though they all have default values/tags. If you find puzzling in adjusting them, please refer to those notes after each parameter.

- **K** (default=2000) Max particles to pick in each micrograph.
  
>If you have a coarse knowledge about how many particles in your micrographs in average, we recommend you to use this average. If not, we recommend you use a relatively big **K** on several micrographs and compute 2D classification. And you can change **K** according to 2D classification results.

- **vis_thresh** (default=0) Threshold for particles to be stored. You can change it from 0.0 to 1.0.
  
>If you find too many particles are included, you can use a higher threshold like 0.3. Empirically, we recommend you use 0.1 for most normal datasets, 0.05 or even smaller for dense datasets.

- **edge** (default=25) Distance between regions to pick and borders of micrographs
  
>Usually those particles lying at the edge cannot be use for reconstruction, and this parameter help with ignoring those marginal particles. Notice that this value refers to a distance on a downsampled micrograph whose width(x-axis) has been downscaled to 1024.

- **min_distance** (default=0)
  
- **visual**
  
- **output_type** (default=thi)
  
## Training

### Parameters

## Supplementary

### THI file

<div id="thi"></thi>
