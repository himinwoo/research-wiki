---
category: lidar-calib
source_papers:
  - "cattaneo-2025-cmrnext-camera-to-lidar-matching-in"
related_pages:
  - "wiki/lidar-calib/domhof-2019-an-extrinsic-calibration-tool.md"
  - "wiki/online-calib/index.md"
tags:
  - lidar-camera-calibration
  - extrinsic-calibration
  - monocular-localization
  - cross-modal-matching
  - optical-flow
  - sensor-agnostic
  - deep-learning
last_updated: 2026-05-12
---

# CMRNext: Camera to LiDAR Matching in the Wild for Localization and Extrinsic Calibration

## Overview

CMRNext (Cattaneo & Valada, 2025) is a sensor-agnostic deep learning approach for camera-LiDAR matching that targets two closely related tasks: monocular localization within pre-built LiDAR maps and extrinsic camera-LiDAR calibration. Unlike most prior learning-based methods, CMRNext generalizes across different camera intrinsics, LiDAR resolutions, and environments in a zero-shot manner without retraining or fine-tuning.

## Key Methods / Concepts

### Optical Flow Formulation for Cross-Modal Matching
CMRNext reformulates the point-pixel matching problem as optical flow estimation. Given a camera image and a synthesized depth image (LiDAR-image) projected from the map at an initial pose, the network predicts dense pixel displacements that align the two modalities. This pixel-space formulation removes any dependency on camera intrinsic parameters during training.

### Two-Stage Pipeline
1. **Matching Stage**: A CNN extracts features from both the RGB image and the LiDAR-image. A 4D cost volume is built using dot-product similarity, and a GRU-based update operator iteratively refines the predicted flow and per-pixel uncertainty.
2. **Pose Regression Stage**: Robust PnP (EPnP + RANSAC) is applied on the established 2D-3D correspondences to recover the 6-DoF camera pose. Camera intrinsics are only required at this second stage.

### Fourier Positional Encoding
Depth values in the LiDAR-image are encoded with Fourier features to help the coordinate-based network learn high-frequency functions, improving matching accuracy.

### Iterative Refinement for Large Displacements
Multiple network instances are trained with different initial error ranges. During inference, the system selects the most appropriate model based on estimated overlap between the camera and LiDAR fields of view, enabling convergence even when the initial pose is far from the true solution.

## Related Papers

- **Cattaneo & Valada (2025)** — CMRNext (this paper).
- **Domhof et al. (2019)** — An extrinsic calibration tool for radar, camera, and LiDAR setups, providing a classical baseline for target-based calibration.

## Connections & Compounding

- **Sensor Agnosticism**: By decoupling the learning problem from metric camera parameters, CMRNext can be deployed on diverse robotic platforms (self-driving cars, quadrupeds, quadcopters) without dataset-specific adaptation. This is a significant departure from methods like CMRNet++ that bake intrinsics into the network.
- **Bridging Localization and Calibration**: The same network handles both monocular localization in LiDAR maps and extrinsic calibration, suggesting that these tasks share a common geometric core: establishing dense correspondences between 2D image pixels and 3D LiDAR points.
- **Potential for Online Calibration**: While not yet real-time, CMRNext's sensor-agnostic nature makes it a promising candidate for future online calibration systems where continuous refinement of extrinsic parameters is needed.

## Open Questions

- Can the optical flow formulation be extended to other cross-modal pairs (e.g., radar-camera or event-camera to LiDAR)?
- How much would performance degrade if trained on data without ground truth poses, using only weak or self-supervision?
- What architectural changes are needed to achieve real-time inference for online calibration use cases?
- Can the method generalize to off-road, indoor, or aerial environments beyond urban autonomous driving?
