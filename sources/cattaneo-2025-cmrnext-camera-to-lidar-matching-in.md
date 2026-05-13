---
source_collection: papers
status: summarized
paper_type: research
pdf_filename: "cattaneo-2025-cmrnext-camera-to-lidar-matching-in.pdf"
pdf_path: "papers/cattaneo-2025-cmrnext-camera-to-lidar-matching-in.pdf"
added_date: 2026-05-12
read_date: 2026-05-12
url: https://arxiv.org/abs/2402.00129
code_url: http://cmrnext.cs.uni-freiburg.de
doi:
authors:
  - Daniele Cattaneo
  - Abhinav Valada
title: "CMRNext: Camera to LiDAR Matching in the Wild for Localization and Extrinsic Calibration"
journal: IEEE Transactions on Robotics
year: 2025
volume:
pages:
publisher: IEEE
abstract: "LiDARs are widely used for mapping and localization in dynamic environments. However, their high cost limits their widespread adoption. On the other hand, monocular localization in LiDAR maps using inexpensive cameras is a cost-effective alternative for large-scale deployment. Nevertheless, most existing approaches struggle to generalize to new sensor setups and environments, requiring retraining or fine-tuning. In this paper, we present CMRNext, a novel approach for camera-LiDAR matching that is independent of sensor-specific parameters, generalizable, and can be used in the wild for monocular localization in LiDAR maps and camera-LiDAR extrinsic calibration. CMRNext exploits recent advances in deep neural networks for matching cross-modal data and standard geometric techniques for robust pose estimation. We reformulate the point-pixel matching problem as an optical flow estimation problem and solve the Perspective-n-Point problem based on the resulting correspondences to find the relative pose between the camera and the LiDAR point cloud. We extensively evaluate CMRNext on six different robotic platforms, including three publicly available datasets and three in-house robots. Our experimental evaluations demonstrate that CMRNext outperforms existing approaches on both tasks and effectively generalizes to previously unseen environments and sensor setups in a zero-shot manner."
tags:
  - lidar-camera-calibration
  - extrinsic-calibration
  - monocular-localization
  - cross-modal-matching
  - optical-flow
  - sensor-agnostic
  - deep-learning
---

## Paper Summary

CMRNext addresses camera-to-LiDAR matching for two tasks: monocular localization within pre-built LiDAR maps and extrinsic camera-LiDAR calibration. The key innovation is a sensor-agnostic formulation that generalizes across different camera intrinsics, LiDAR models, and environments without retraining or fine-tuning.

## Key Contributions

- **Sensor-Agnostic Design**: The matching network operates purely in pixel space, making it independent of camera intrinsic parameters during training. Intrinsics are only needed at the PnP stage.
- **Optical Flow Formulation**: Reframes point-pixel matching as optical flow estimation between a camera image and a synthesized "LiDAR-image" (depth projection).
- **Iterative Refinement**: Trains multiple network instances with different initial error ranges; at inference, the appropriate model is selected based on estimated overlap.
- **Strong Generalization**: Evaluated zero-shot on six robotic platforms (three public datasets: KITTI, Argoverse; three in-house robots: self-driving car, quadruped, quadcopter) without dataset-specific fine-tuning.
- **State-of-the-Art Results**: Outperforms existing methods on both localization and extrinsic calibration benchmarks.

## Problem Statement

Existing learning-based camera-LiDAR matching methods are typically tied to specific sensor configurations (camera intrinsics, LiDAR resolution) and require retraining or fine-tuning when deployed on new robots or in new cities. This limits scalability for large-scale deployment where cost and diversity of sensor setups are critical.

## Methodology

1. **LiDAR-Image Synthesis**: Project the LiDAR map into a virtual image plane at an initial pose estimate to create a depth image.
2. **Feature Extraction**: Use a CNN (ResNet-based) to extract features from both the RGB image and the LiDAR-image.
3. **Cost Volume & Matching**: Construct a 4D cost volume using dot-product similarity and apply a GRU-based update operator to predict dense pixel displacements (optical flow) iteratively.
4. **Positional Encoding**: Encode depth values with Fourier features to help the network learn high-frequency functions.
5. **Uncertainty Estimation**: The network predicts both flow and per-pixel uncertainty, supervised with negative log-likelihood loss.
6. **Pose Regression**: Use robust PnP (EPnP + RANSAC) on the established 2D-3D correspondences to recover the 6-DoF camera pose.

## Results

- **KITTI Odometry Seq 00**: Median translation error of 6.21 cm and rotation error of 0.23° for localization.
- **Extrinsic Calibration**: Achieves high accuracy across diverse sensor setups.
- **Zero-Shot Generalization**: Same trained model works on KITTI (Germany), Argoverse (USA), and three different in-house robots without fine-tuning.
- **Ablation Studies**: Validated architectural choices including Fourier features, iterative refinement, and uncertainty weighting.

## Limitations & Future Work

- **Domain Restriction**: Trained exclusively on autonomous driving data; generalization to off-road or mining environments is untested and likely limited.
- **Real-Time Performance**: Faster than prior camera-agnostic methods but not yet capable of real-time operation.
- **Training Requirements**: Needs ground truth poses and accurate camera intrinsics for supervision, which may be hard to obtain in all deployment scenarios.

## Personal Notes

- The decomposition into pixel-space matching + geometric pose estimation is elegant and enables the sensor-agnostic property.
- The use of optical flow for cross-modal matching is a notable reframing that simplifies the learning problem.
- Could be relevant for online-calib if extended to real-time performance and continuous refinement.
- Code and pretrained models are publicly available, which is excellent for reproducibility.
