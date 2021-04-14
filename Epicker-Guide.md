# EPicker Guide

---
>Last editted by Tianfang, 2021.4.13

## Overview

### What is EPicker

EPicker is a standalone software for particle picking in cryo-EM. It is implemented based on CenterNet, an open-source deep learning detection framework. Trained and well tested on datasets containing heterogenous particles, EPicker is able to pick particles, vesicles and fibers effectively. Different from other particle picking tools, EPicker builds up a continual learning mechanism. With the introduction of an exemplar generated from old datasets, EPicker accumulates knowledge during the continual training process and avoids catastrophic forgetting, yielding a new model able to recognize both new particles and old ones. With this advantage, you can continually upgrade your model and save a lot of storage for model saving and data saving used for traditional retraining procedures.

### About this guide

This user guide includes all instructions you may need for installation, picking and training. A tutorial is also provided at the end of this guide to help you walk through the complete workflow of EPicker. If you have any questions or if anything goes wrong, please contact me at ***zhaotianfang2010@163.com***

## Download and Installation

### Requirements

#### Hardware

A GPU is essential to run a deep learning program. We have tested EPicker on following GPUs.  

- NVIDIA RTX 2080Ti  
- NVIDIA Tesla P100  
- NVIDIA Tesla A100  
- NVIDIA Titan X

#### System

We have tested EPicker on following systems.

- CentOS 7
- Ubuntu 18.04
- Ubuntu 16.04

A version for Windows is not available, but it's coming soon.

#### GCC and CUDA

We use pytorch as our deep learning framework. And because pytorch does not have a version that is compatible with all CUDA versions, we provide installation packages for different environments. For an old version EPicker, CUDA9.0 and G++ 4.8.5 are enough to conduct installation. For higher versions, G++>=5.0 and CUDA10+ are needed.

#### Network

Considering some GPU nodes may not have access to Internet, we additionally provide offline packages. All third party dependencies are included in this installation script. You only need to download an appropriate package and simply run it in your terminal.

Certainly, if your machine is Internet-available, you also need to download an installation script. But third party dependencies will be downloaded automatically through the installer according to your local envrionment.

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

You can specify which g++ to use. If not specified, default g++ in your environment will be used.
>$./EPicker-cu110.sh {Path to g++}

## Picking

### Picking command

Picking script is in Epicker/Epicker/bin/
>$cd Epicker/Epicker/bin/  

>$./epicker.sh {Parameters}

### Essential Parameters

Set your input and ouput path, select your model and switch to a working mode using these essential parameters.

- **data** Micrographs to be picked, which can be a mrc file, a path to mrc files or a thi file. For information of THI file, please refer to [THI file](#thi).
- **load_model** Pretrained model. Three general models for picking particles, liposomes and filaments are available at [EPicker models]().
- **mode** (default=particle) Picking mode: particle, vesicle or fiber.
- **output** Folder of output coordinate files.

### Optional parameters

These parameters are also useful and may influence your final picking results. You may change them to fit your own picking jobs, though they all have default values/tags. If you find puzzling in choosing a proper set of parameters, please refer to those notes after each parameter.

- **K** (default=2000) Max particles to pick in each micrograph.
  
>If you have a coarse knowledge about how many particles in your micrographs in average, we recommend you to use this average. If not, we recommend you use a relatively big **K** on several micrographs and compute 2D classification. And you can change **K** according to 2D classification results.

- **vis_thresh** (default=0) Threshold for particles to be stored. You can change it from 0.0 to 1.0.
  
>If you find too many particles are included, you can use a higher threshold like 0.3. Empirically, we recommend you use 0.1 for most normal datasets, 0.05 or even smaller for dense datasets.

- **edge** (default=25) Distance between regions to pick and borders of micrographs
  
>Usually those particles lying at the edge cannot be use for reconstruction, and this parameter help with ignoring those marginal particles. Notice that this value refers to a distance on a downsampled micrograph whose width(x-axis) has been downscaled to 1024.

- **min_distance** (default=0) Minimum distance between two particles.

>In most cases, you do not need to set this value. If you find multiple boxes are addressed to only one particle, you may use a bigger min_distance.

- **ang_thresh** (default=0.3) Maximum curvature of tracing fiber. 

>If you are picking particles and vesicles, you do not need to set this value. If you are picking and tracing fibers, you can change this parameter from 0.0 to 1.57 rad to get the best tracing results.

- **visual** EPicker will visualize its picking results and save them in PNG format to your disk if you add **--visual** to your command.
  
- **output_type** (default=thi) Which format of coordinate file you want to save. By now, for liposomes and filaments, we only support THI format. For single-particle jobs, you can switch to star, box, coord at your convinience.

- **gpus** (default=0) Which gpu to use.

> EPicker does not support picking using multiple gpus.

## Training

You can train your model use your own data and annotations. [Continual training](#continual_training) requires some addtional parameters than training from scratch.

### Training command

Training script is in Epicker/Epicker/bin/
>$cd Epicker/Epicker/bin/  

>$./epicker_train.sh {Parameters}

### Essential parameters(training)

Set your path to data and labels, choose your label format and working mode and name your job using these essential parameters.

- **data** Image list(.thi) or a folder that contains micrographs used for training.

- **label** Path to coordinate files.

- **label_type** (default=thi) Format of your label files, thi/star/box/coord.

- **exp_id** The output model will be saved in **exp_id** as model_last.pth.

- **mode** (default=particle) Training mode: particle, vesicle or fiber.

### Optional parameters(training)

These parameters allow you to adjust your training procedure. Usually, you don't have to change the default settings unless you meet problems. If you find puzzling in choosing a proper set of parameters, please refer to those notes after each parameter.

- **load_model** You can load an old model by adding **--load_model XXX.pth** to your command.

> If you are training a model from scratch, just ignore this parameter. If you're finetuning a model or training in a continual manner, it helps you initialize your new model using old weights.

- **lr** (default=1e-4) Learning rate, the stepsize of gradient descent.

> Empirically, you dont't need to change it.

- **batch_size** (default=4) Batchsize, number of images in a mini-batch.

> Batchsize should not exceed your GPU memory. For example, if you're using a Tesla P100, you can set **batch_size=4**. If you have 4 Tesla P100 GPUs, set **batch_size=16**.

- **num_epoch** (default=140) Total epochs for training.

- **train_pct** (default=80) Percentage of data used for training.

- **test_pct** (default=20) Percentage of data used for testing.

> EPicker has a testing module based on COCOAPI, with which you can quickly compute your precision, recall and draw a PR-curve.

- **gpus** (default=0) Specify which gpus you would like to run your training jobs on. GPU ids should be divided with comma like **--gpus 0,1,2,3**, if you want to train on the first 4 gpus of your machine.

> Training on multiple gpus will be faster than training on only one. However, training on multiple gpus will cause a slight decay in performance. Yet we haven't come up with a solution to it. **So we recommend you to use just one gpu.**

- **sparse_anno** If your annotations are sparse, add **--sparse_anno** to your command.

> Sparse annotations means many positive samples are missed in your coordinate files. Training on such datasets is also known as "positive unlabeled learning". Addressing this option will activate a positive unlabeled method in EPicker.

<div id="continual_training"></div>

### Continual training

Continual training process of EPicker enables incrementally adding new features to the old model. Constrained by the exemplar dataset and extra terms of loss function, the new model performs well both on the new dataset and the old datasets. After training on a new dataset, EPicker will extract a small set of particles from the new dataset and update the old exemplar dataset. Following parameters are essential for continual training.

- **load_model** Path to old model.

- **sampling_size** (default=200) Number of sample particles selected from the training dataset to construct an exemplar.

- **load_exemplar** Path to the old exemplar dataset.

- **output_exemplar** Path to save the new exemplar dataset.

> After each continual training process, EPicker outputs a new exemplar dataset updated on the current training dataset.

- **continual** If you are training in a continual manner, add **--continual** to your command.

## Supplementary

<div id="thi"></div>

### THI file

THI files are broadly used in EPicker workflow. All THI files are in a general format like this:
>[Header]
>
>content
>
>[End]

The header declares what kind of content is stored in a THI file. Each line should be a coordinate of a particle, a path to an mrc file or an endpoint of a piece of filament. Examples of all types of THI file are shown below.

**THI file of particles.** X, Y and Value stand for center coordinates and confidence(score of the particle).

>[Particle Coordinates: X Y Value]
>
>1243 512 0.43
>
>123 5123 0.39
>
>[End]

**THI file of vesicles.** Radius stands for the radius of a vesicle. Multiply Radius and pixelsize, and you will get the physical size of a vesicle.

>[Vesicle Coordinates: X Y Radius Value]
>
>4313 1523 30 0.32
>
>3323 4152 60 0.28
>
>[End]

**THI file of fibers.** X Y stands for center coordinates of endpoints. Endpoints with same Group belongs to one fiber.

>[Helix Coordinates: X Y Group Value]
>
>123 456 1 0.54
>
>156 499 1 0.49
>
>2231 2210 2 0.47
>
>2456 1982 2 0.46
>
>[End]

**THI file of image list.** You can specify which mrc files to use without creating a new folder and copy them to it.

>[File List: Filename]
>
>/Data/stack_0001_DW.mrc
>
>/Data/Stack_0002_DW.mrc
>
>[End]
