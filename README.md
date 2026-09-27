# VoxTerra — AI-Powered Autonomous Mine Rescue Rover

VoxTerra is a prototype system for autonomous gas detection, source-tracking, and rescue support in underground mines. It combines a MATLAB-based AI pipeline (gas mapping, gradient-based source estimation, and machine-learning gas-behavior classification) with browser-based 3D and mesh-networking simulations, a human-detection module, and CAD models of the physical rover.

Built for **Smart India Hackathon (SIH) 2026**.

**🔴 Live Demo:** [voxterra-mine-rescue.onrender.com](https://voxterra-mine-rescue.onrender.com/)

> Hosted on Render's free tier — if the app has been idle, the first load can take 30–50 seconds to spin up. The demo brings together the mine safety map, AI gas-behavior pipeline, rover fleet control, and mission event log into one control-room style dashboard.



## Overview

Underground mines are prone to hazardous gas leaks (methane, CO, CO₂) that threaten worker safety and complicate rescue operations after an incident. VoxTerra proposes a fleet of autonomous rovers that:

1. Patrol a mine and collect multi-gas sensor readings (CH₄, CO₂, CO, O₂, temperature) over repeated visits.
2. Build a spatial gas concentration map and compute local gas gradients.
3. Estimate the gas leak's source location and track how it evolves (spreading, accumulating, dissipating, or stable) using a trained AI classifier.
4. Autonomously navigate toward the estimated source to support rescue teams.
5. Relay hazard data back to the surface through a multi-hop rover mesh network, so information keeps flowing even if a rover is disabled (e.g., by a rockfall).
6. Detect trapped or injured workers in camera footage using object detection.

## Repository Contents

| File | Description |
|---|---|
| `4_MineRover_AI_Final_matlab.m` | Core MATLAB prototype: simulates a 20×20 mine grid, generates multi-gas sensor data across two rover visits, computes gas gradients and source estimation, trains a decision-tree AI model to classify gas behavior, and drives autonomous rover navigation toward the estimated source. Ends with a full 4-panel rescue dashboard. |
| `5_MineRover_emergency_scenario_matlab.m` | Extended emergency scenario: simulates a worsening gas leak across 5 rover visits, detects emergency progression (falling O₂ trend), and re-runs the AI gas-behavior pipeline and rover navigation under active-emergency conditions. |
| `6_MineRover_HumanDetection.m` | Human/worker detection module using a pretrained YOLOv4 (CSP-Darknet53-COCO) object detector on rover camera images, annotating detected workers with confidence scores. |
| `1_voxterra-rover-simulation.html` | Interactive 3D rover simulation (Three.js) — visualizes the physical rover model navigating a mine environment in-browser. |
| `2_voxterra-mesh-sim.html` | Interactive mesh-relay network simulation — visualizes multiple rovers relaying hazard data hop-by-hop to a surface gateway, including a rockfall scenario that disables a rover and shows the mesh dynamically rerouting. |
| `3_CAD_VoxTerra.png` | CAD render of the physical tracked rover platform (sensor pods, PTZ camera, LIDAR/antenna mast, tracked chassis). |
| `7_VOXTERRA__Gas_AI_Mine_Rescue_Dashboard.pdf` | Final rescue dashboard output: live CH₄ map, AI gas-behavior prediction, autonomous rover path, and rescue status readout. |
| `8_AI_Gas_Behavior_Map.pdf` | Spatial map of AI-predicted gas behavior classes (Stable / Dissipating / Accumulating / Spreading) across the mine grid. |
| `9_Autonomous_Rover_Gas_Tracking.pdf` | Visualization of the rover's autonomous path as it tracks the gas concentration gradient toward the estimated leak source. |
| `10_AI_Model_Confusion_Matrix.pdf` | Confusion matrix for the gas-behavior classifier evaluated on unseen synthetic test data. |
| [Live Demo](https://voxterra-mine-rescue.onrender.com/) | Deployed "Mine Rescue Control" web dashboard — unifies rover fleet control, the living mine safety map (CH₄ heatmap / behavior / rover paths), the 15-stage AI analysis pipeline, temporal gas comparison, AI risk intelligence, priority alerts, and a mission event log into one interface. |

## How It Works

**1. Sensor simulation** — Synthetic multi-gas readings (CH₄, CO₂, CO, O₂, temperature) are generated across a mine grid using distance-based exponential decay from a hidden gas source, with added sensor noise, across multiple rover visits.

**2. Gas mapping & gradient analysis** — Readings are interpolated into a spatial concentration map; local North/South/East/West gradients around the rover's position are used to estimate the gas source direction and distance.

**3. Temporal & spread analysis** — Comparing readings across visits yields gas change rates, affected-area radius growth, and an estimated spread rate (grid units/second).

**4. AI gas-behavior classification** — A decision tree (`fitctree`) trained on 8 engineered features (CH₄ level, CH₄ change, spread rate, O₂ change, temperature change, CO₂, CO, gradient magnitude) classifies current conditions into one of four behaviors: **Stable, Dissipating, Accumulating, Spreading**. The model is validated on a 70/30 train/test split (see confusion matrix), achieving high test accuracy.

**5. Autonomous navigation** — The rover moves step-by-step toward the estimated gas source using a normalized-direction pursuit algorithm, stopping once it reaches the source region.

**6. Mesh relay network** — Multiple rovers form a dynamic multi-hop mesh back to a surface gateway; a roaming rover can bridge gaps if a fixed relay is disabled by a hazard (e.g., rockfall), keeping gas data flowing to the surface.

**7. Human detection** — Rover camera frames are passed through a YOLOv4 object detector to flag and localize workers who may need rescue.

## Running the MATLAB Scripts

Requires MATLAB with the following toolboxes:
- Statistics and Machine Learning Toolbox (`fitctree`, `cvpartition`, `confusionchart`)
- Computer Vision Toolbox / Deep Learning Toolbox (`yolov4ObjectDetector`, for human detection only)

```matlab
% Run the core AI mine rescue pipeline
4_MineRover_AI_Final_matlab.m

% Run the multi-visit emergency escalation scenario
5_MineRover_emergency_scenario_matlab.m

% Run human detection on a rover camera image (prompts for image file)
6_MineRover_HumanDetection.m
```

## Running the Web Simulations

Both HTML files are self-contained and can be opened directly in any modern browser — no build step or server required:

- `1_voxterra-rover-simulation.html` — 3D rover navigation simulation
- `2_voxterra-mesh-sim.html` — Mesh relay network simulation with rockfall scenario

## Live Demo

**[voxterra-mine-rescue.onrender.com](https://voxterra-mine-rescue.onrender.com/)**

A deployed, all-in-one control-room dashboard that brings the MATLAB AI pipeline and rover/mesh simulations together into a single interactive web app:

- **Rover fleet panel** — dispatch/return controls for a 3-rover field unit fleet
- **Living mine safety map** — CH₄ heatmap, AI-predicted gas behavior overlay, and rover path tracking, with Normal / Emergency Drill scenario modes
- **15-stage analysis pipeline** — a live, step-by-step visualization mirroring the MATLAB gas-mapping → gradient → source estimation → AI classification pipeline
- **Temporal comparison** — gas trend readout across rover visits
- **AI risk intelligence** — predicted gas behavior class with an associated risk level and rescue confidence score
- **Operations panel** — priority alerts and a mission event audit trail
- Dedicated actions to **Run AI Gas Analysis**, **Locate Gas Source**, and **Scan for Survivor**

> Hosted on Render's free tier — if idle, the first load can take 30–50 seconds while the instance spins up.

## Disclaimer

This is a prototype/proof-of-concept built for a hackathon. Gas sensor data, mine layouts, and gas-behavior training data are synthetically generated for demonstration purposes and have not been validated against real mine sensor hardware or field conditions.
