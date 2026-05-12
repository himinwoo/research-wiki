---
category: multi-sensor
source_papers:
  - "domhof-2019-an-extrinsic-calibration-tool"
related_pages:
  - "wiki/lidar-calib/"
  - "wiki/radar-calib/"
  - "wiki/camera-calib/"
tags:
  - multi-sensor-calibration
  - radar-camera-lidar
  - extrinsic-calibration
  - target-based
  - ICRA
last_updated: 2026-05-12
---

# An Extrinsic Calibration Tool for Radar, Camera and Lidar

## Overview

Domhof et al. (2019) present the first open-source tool for **joint extrinsic calibration of radar, camera, and LiDAR** — three modalities with fundamentally different measurement principles. Existing tools only support pairwise calibration (at most two sensors), which forces practitioners to chain estimates and accumulate error. This work solves the problem by (1) extending a standard calibration target to be detectable by all three sensors, and (2) proposing a flexible optimization framework with three graph-based configurations.

## Key Methods / Concepts

### Multi-Modal Calibration Target
The target builds on the circular-hole plate design used by Velas et al. and Guindel et al. for LiDAR-camera calibration. Domhof et al. augment it with a **trihedral corner reflector** (high Radar Cross Section) so that radar can also detect the target. This yields simultaneous measurements for:
- **Camera**: circular holes via image processing.
- **LiDAR**: circular holes via point-cloud circle fitting.
- **Radar**: trihedral reflector via RCS peak detection.

### Three Optimization Configurations
1. **MCPE — Minimally Connected Pose Estimation**  
   Calibrate each sensor pairwise against a chosen reference sensor. Conceptually a tree graph. Simple and fast, but errors propagate along chains.

2. **FCPE — Fully Connected Pose Estimation**  
   Include error terms for **all sensor pairs** and add loop-closure consistency constraints. Conceptually a complete graph. Best performance for LiDAR-radar calibration when more than five board locations are used.

3. **PSE — Probabilistic Structure Estimation**  
   Jointly estimate sensor poses **and** target (board) poses in a probabilistic model, similar to structure-from-motion or bundle adjustment. Uses an EM-style iteration to estimate noise covariances. Homogeneous error metric but no explicit loop closure.

### Radar-Specific Handling
- Radar provides 2D polar measurements (range, azimuth) with no reliable elevation.
- The tool projects expected 3D corner-reflector positions to the radar plane by discarding elevation.
- FOV constraints are enforced as inequality constraints during SLSQP optimization.
- Initialization assumes zero elevation; Kabsch algorithm provides the initial pose.

## Related Papers

- **Velas et al.** — LiDAR-monocular camera calibration using circular holes (baseline target design).
- **Guindel et al.** — LiDAR-stereo camera calibration using the same target; Domhof et al. reproduce identical results for validation.
- **Persic et al.** — Radar-LiDAR calibration using triangular styrofoam targets with trihedral reflectors (inspired the radar-visible extension).

## Connections & Compounding

- **SLAM analogy**: FCPE borrows the loop-closure concept from pose-graph SLAM. Treating sensors as "nodes" and pairwise constraints as "edges" makes the optimization intuition familiar.
- **Probabilistic formulation**: PSE connects to bundle adjustment and structure-from-motion literature, where both camera (sensor) poses and 3D structure (target poses) are unknowns.
- **Target design trade-off**: The trihedral reflector adds radar visibility but keeps the target compact. This is a pragmatic compromise between precision (corner reflector RCS) and manufacturability (flat plate).

## Open Questions

- Can the target design be further miniaturized for indoor or close-range calibration?
- Would online/targetless calibration eventually supersede target-based methods for production vehicles?
- How does FCPE scale to >3 sensors (e.g., multi-LiDAR + multi-radar + multi-camera)? The graph density grows quadratically.
