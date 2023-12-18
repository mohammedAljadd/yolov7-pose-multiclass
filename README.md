# YOLOv7 Pose Bug Fixes for Multiclass Detection

This repository contains bug fixes made to the YOLOv7 pose official source code by enabling multiclass detection. The changes made specifically address issues related to :
    
    - kpt loss calculation
    - Cls loss calculation
    - Non max suppression 


NB: The only changes made here are bug fixes aimed at enabling multiclass detection.

Here is the link to the original GitHub repository: [YOLOv7 Official Repository](https://github.com/WongKinYiu/yolov7/tree/pose)

License: This repository is released under the GPL-3.0 license in compliance with the original YOLOv7 pose source code.


Please note: This repository was created as an attempt to address certain issues and improvements in the original codebase. Due to the original authors' limited activity in reviewing or merging pull requests, this fork was established to facilitate ongoing development and community contributions



### YOLOv7 Pose Model Overview

The YOLOv7 Pose model builds upon the YOLO (You Only Look Once) architecture, aiming at object detection while emphasizing the precise estimation of human poses. This adaptation expands the original YOLOv7 framework by seamlessly incorporating the ability to estimate human body positions within its detection system

## Key Features
**Topology**: YOLOv7 Pose excels in detecting up to 17 keypoints across the human body. During transfer learning, users have the flexibility to select specific keypoints of interest by masking points with lower visibility.

**Multi-person Detection**: YOLOv7 Pose offers support for detecting multiple persons within a single image, enhancing its applicability in crowded scenes or group scenarios.

**Real-time Inference**: Leveraging optimized processes, YOLOv7 Pose demonstrates exceptional real-time performance, ensuring swift and efficient processing.

## Repository's Added Feature
Following the resolution of several bugs in the original source code, this repository now enable YOLOv7 Pose to detect **multiple classes** instead of being limited to a single class (e.g., 'Person'). Suppose your classes include 'Person', 'Dog', and 'Cat'. In that case, the model will generate outputs comprising the detected class label alongside the 17 associated keypoints for each recognized object.

## Architecture

The following image contains the Yolov7 pose architecture :
<img src="architecture\pose_architecture.png" width="600">

Let's break down each stage of the model's architecture :

#### Backbone
The backbone serves as the primary part of the network responsible for extracting visual features from the image. In this instance, we employ the **Darknet-CSP** as the backbone—a Convolutional Neural Network (CNN) tailored specifically for visual feature extraction. The term "**CSP**" refers to **Cross-Stage Partial Network**, a design technique enhancing the model's performance. This backbone utilizes a strategy from the **CSPNet** model (Wang et al., 2019) to split the feature map into two distinct parts and then merge them, potentially providing a more comprehensive image representation.

#### PANet Feature Fusion
This method, employed in instance segmentation, is used to merge features. **PANet** addresses the issue of scale differences among objects in an image. It uses a pyramid of resolution levels to account for objects at different scales.

#### Detection Heads
Responsible for predicting detected objects and associated information, including classes, keypoints, and bounding boxes.

#### Output Tensors
There are 4 output tensors to consider different object scales. Each vector in the tensors contains the following information:

$P_v$ = $Obj_{conf}$, $C_{x}$, $C_{y}$, W, H, $class_{conf1}$, ..., $class_{conf9}$, $K^{1}\_x$,  $K^{1}\_y$, $K^{1}_{conf}$, ..., $K^{17}\_x, K^{17}\_y, K^{17}\_{conf}$





- $Obj_{conf}$ : Probability of being an object 'objectness'.
- $C_x, C_y, W, H$: Bounding box coordinates.
- $class_{confi}$ : Probability that the object belongs to ith class.
- $K_x, K_y, K_{conf}$ : Coordinates (x, y) of a point among the 17 points, with conf as the probability. $K_{conf}$ denotes the visibility of keypoints, and points with low visibility can be discarded based on this visibility indicator.




## Loss function

The loss function is as follows:

$L_{total} = \sum_{s,i,j,k} (\lambda_{cls} \cdot L_{cls} + \lambda_{box} \cdot L_{box} + \lambda_{kpts} \cdot L_{kpts} + \lambda_{kpts\_conf} \cdot L_{kpts\_conf})$

The loss function is a sum over:

- $s$: The scale. YOLO detects objects across multiple scales (multi-head detection).
- $i$: The x-coordinate of the grid cell responsible for detecting the object.
- $j$: The y-coordinate of the grid cell responsible for detecting the object.
- $k$: The $k^{th}$ anchor.

The sub-losses are:

- $L_{cls}$: The loss incurred from correctly classifying the objects.
- $L_{box}$: The loss associated with bounding boxes.
- $L_{kpts}$: The loss related to detecting keypoint coordinates.
- $L_{kpts\_conf}$: The loss in classifying keypoints, particularly their visibility.



In the original [YOLOv7 Pose paper](https://arxiv.org/abs/2204.06806), they set $\lambda_{cls}$ = 0.5, $\lambda_{box}$ = 0.05, $\lambda_{kpts}$ = 0.1 and $\lambda_{kpts\_conf}$ = 0.5.

### License

These modifications are distributed under the terms of the GPL version 3.0. For more details, refer to the COPYING file included in this repository, which contains the full text of the GPL license.
