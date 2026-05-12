---
source_collection: papers
status: summarized
paper_type: research
pdf_filename: "domhof-2019-an-extrinsic-calibration-tool.pdf"
pdf_path: "papers/domhof-2019-an-extrinsic-calibration-tool.pdf"
added_date: 2026-05-12
read_date: 2026-05-12
url:
code_url:
doi:
authors:
  - Joris Domhof
  - Julian F. P. Kooij
  - Dariu M. Gavrila
title: "An Extrinsic Calibration Tool for Radar, Camera and Lidar"
journal: "2019 International Conference on Robotics and Automation (ICRA)"
year: 2019
volume:
pages: "8107-8113"
publisher: "IEEE"
abstract: "We present a novel open-source tool for extrinsic calibration of radar, camera and lidar. Unlike currently available offerings, our tool facilitates joint extrinsic calibration of all three sensing modalities on multiple measurements. Furthermore, our calibration target design extends existing work to obtain simultaneous measurements for all these modalities. We study how various factors of the calibration procedure affect the outcome on real multi-modal measurements of the target. Three different configurations of the optimization criterion are considered, namely using error terms for a minimal amount of sensor pairs, or using terms for all sensor pairs with additional loop closure constraints, or by adding terms for structure estimation in a probabilistic model. The experiments further evaluate how the number of calibration boards affect calibration performance, and robustness against different levels of zero mean Gaussian noise. Our results show that all configurations achieve good results for lidar to camera errors and that fully connected pose estimation shows the best performance for lidar to radar errors when more than five board locations are used."
tags:
  - multi-sensor-calibration
  - radar-camera-lidar
  - extrinsic-calibration
  - target-based
  - ICRA
---

## Paper Summary

This paper introduces an open-source extrinsic calibration tool capable of jointly calibrating radar, camera, and LiDAR — a combination not previously addressed by existing tools. The key innovation is a calibration target design that enables simultaneous detection across all three modalities, paired with a flexible optimization framework that supports three different graph configurations for multi-sensor pose estimation.

## Key Contributions

1. **Joint three-sensor calibration tool**: First open-source tool to calibrate radar, camera, and LiDAR jointly, rather than pairwise.
2. **Multi-modal calibration target**: Extended target design based on circular holes and trihedral corner reflectors, detectable by camera, LiDAR, and radar simultaneously.
3. **Three optimization configurations**:
   - **MCPE (Minimally Connected Pose Estimation)**: Pairwise calibration toward a single reference sensor (tree graph).
   - **FCPE (Fully Connected Pose Estimation)**: All sensor pairs included with loop-closure constraints (complete graph).
   - **PSE (Probabilistic Structure Estimation)**: Structure-from-motion style joint estimation of target poses and sensor extrinsics with EM-like covariance estimation.
4. **Real-world experimental validation**: Tested on a Toyota Prius with Velodyne HDL-64E, Continental ARS430 radar, and stereo cameras.

## Problem Statement

Existing calibration tools only support pairwise calibration of at most two sensing modalities (e.g., LiDAR-camera or radar-camera). Calibrating a full multi-sensor suite requires chaining pairwise results, which accumulates error and ignores cross-modal constraints. There is no existing tool or target design that enables simultaneous measurement for radar, camera, and LiDAR.

## Methodology

### Calibration Target
- Extends the circular-hole plate design used by Velas et al. and Guindel et al.
- Adds a trihedral corner reflector for radar detection (high RCS).
- Circular holes visible to camera and LiDAR; trihedral reflector visible to radar.

### Optimization
- **Error metric**: Squared Euclidean distance between corresponding target features (circle centers for LiDAR/camera, projected 3D corner reflector position for radar).
- **Radar-specific handling**: Projects 3D points to radar plane (spherical coordinates, disregarding elevation), with FOV constraints enforced via inequality constraints.
- **Optimizer**: SLSQP from SciPy, initialized with Kabsch algorithm.

### Three Configurations
1. **MCPE**: Calibrate each sensor to a reference. Simple, but error propagates along chains.
2. **FCPE**: Optimize all pairwise transformations directly with loop-closure consistency. Best for LiDAR-radar when >5 board locations used.
3. **PSE**: Estimate structure (target poses) and sensor poses jointly in a probabilistic model. Homogeneous error metric but no explicit loop closure.

## Results

- **Board locations**: Calibration accuracy improves with more board locations. FCPE shows best LiDAR-radar performance when K > 5.
- **Noise robustness**: All three configurations are robust to zero-mean Gaussian noise up to σ = 0.06 m in LiDAR observations. MCPE and FCPE perform similarly for LiDAR-camera; FCPE outperforms for LiDAR-radar.
- **Baseline comparison**: MCPE yields identical results to Guindel et al. for single-board LiDAR-stereo calibration, validating correctness.
- **Real-world qualitative**: Calibrated transformations successfully align radar, LiDAR, and camera detections in scene visualization.

## Limitations & Future Work

- The tool has only been validated on a specific sensor setup (Velodyne HDL-64E, Continental ARS430, stereo camera); generalization to other sensor models is not guaranteed.
- PSE (Probabilistic Structure Estimation) is sometimes less robust compared to MCPE and FCPE in practice.
- The calibration target requires manual placement at multiple board locations, which is time-consuming.
- **Future work**: Investigate the effect of more than three sensors on calibration performance, and explore scalability to denser multi-sensor graphs.

- Target must be manually placed at multiple locations; not targetless.
- Radar elevation angle is assumed zero during initialization (planar assumption).
- Probabilistic structure estimation (PSE) is more complex and does not explicitly enforce loop closure.
- Only tested on a single vehicle platform (Toyota Prius).

## Personal Notes

- The FCPE configuration is conceptually similar to pose graph optimization in SLAM, applied to extrinsic calibration. The loop-closure idea translates naturally.
- The target design is practical: reusing the circular-hole plate and adding a corner reflector is a minimal but effective extension.
- Good reference for anyone setting up a radar+LiDAR+camera rig and needing a reproducible calibration pipeline.
