# BFMC (RAVEN Version) - Documentation Project

This repo contains the sources for the Bosch Future Mobility Challenge documentation.
Find it at this [link](https://bosch-future-mobility-challenge-documentation.readthedocs-hosted.com)

Please find here the [Team Raven: Technical Dependency Map](https://docs.google.com/spreadsheets/d/1YjaaoJ-bXWc-HwKyBHRvmbrGb-Hnskfs1KYgbPaIz-Q/edit?usp=sharing)

---

## 🚦 Project Workflow & Dependencies

To manage our team effectively, we utilize a structured numbering system and parallel development tracks.

### 1. The Gap (Issues 009 – 018): The "Hardware Arrival" Phase

The gap in issue numbering is reserved for the **Physical Car Integration** phase once the DHL package arrives. While we are currently in a "No-Car" phase, these missing numbers represent future milestones for:

* **Physical Assembly:** Mounting the camera and sensors.
* **Sensor Calibration:** Moving from virtual camera math to real lens calibration.
* **Real-World PID Tuning:** Adjusting the  and  values on actual asphalt/tiles instead of Gazebo friction.
* **Power Management:** Testing the actual battery sag and voltage rails.

**Task 019a** serves as the bridge; it is the documentation audit performed now to ensure we are ready to fill the 009–018 gap the moment the car is unboxed.

### 2. Sub-task Dependencies (e.g., 008a vs. 008b)

In most cases, "a" must start before "b," but they can be worked on simultaneously once the "handshake" is defined.

* **Sequential Dependency:** For 008a (Capture) and 008b (AI), you cannot train the AI without data. One member takes screenshots (008a) to provide the dataset for the recognition model (008b).
* **Parallel Development:** The member on 008b can write the "Logic Skeleton" (code expecting an image and returning a label) using placeholder images while waiting for data from 008a.

### 3. Inter-Team Parallelism (e.g., 007X vs. 008X)

Work happens in parallel across our 3 Teams:

* **Team C** can finish the Dashboard (007a) using "Mock Data" (fake speed/steer numbers) to test the UI layout.
* **Team A** can be mid-development on the Sign AI (008b).
* **The Connection:** Teams only need to meet at the end to "plug" the real AI output into the Dashboard.

---
## 👥 Team Roster & Task Allocation
For a detailed breakdown of team roles and member contributions, please refer to our [Official Membership & Tasks Sheet](https://docs.google.com/spreadsheets/d/1RilLu0W7pt7jjyVc8MYLopzRnFdB_6Sp1dFgL2LRUzE/edit?usp=sharing).
All development tasks are distributed across the 5-member squad to ensure parallel progress. 

> **Note on Version Control:** To maintain a clean main branch, **Hatem Soliman** acts as the *Integration Lead*, reviewing and committing modules submitted by Team A, B, and C members. Thus, the commit history reflects the integration events rather than individual author timestamps.

## 📋 Kanban Board & Team Structure

Our GitHub Project board uses a specific labeling system to define ownership and task types.

### Team Definitions

* **Team A: Perception & Data (The "Eyes"):** Focused on the vision pipeline, OpenCV, and AI models. They use `raven-brain-stack` (Vision) and `raven-sim`.
* **Team B: Hardware & Low-Level (The "Spine"):** Focused on the physical car, C++ Nucleo code, and PID control. They use `raven-embedded-control` and `bfmc-documentation`.
* **Team C: Integration & Logic (The "Joker"):** The decision-makers who connect Team A to Team B. They write the State Machine (FSM) and Dashboard in `raven-brain-stack` and `raven-computer`.

### How to Read Issues

Each issue on the Kanban board follows this format: `[Issue #] [Task Name] - [Team Label]`.

1. **GENERAL Status:** Foundational tasks (like 001a and 019a) that **everyone** must understand or perform to set up their environment.
2. **SPECIALIZED Status:** Technical features specific to a single team's mission (e.g., PID math for Team B or AI for Team A).

---
## 📝 Summary of Workflow Logic

* **General Tasks (001a, 019a):** Everyone starts here immediately to ensure the "Digital Twin" and "Hardware Map" are understood.
* **Sub-tasks (a, b, c):** These are "Micro-steps" within a feature. Usually, "a" provides the input for "b".
* **Team Parallelism:** Team A, B, and C work at the same time because the **Serial Protocol** acts as a contract between them.


## 🚀 Recently Documented Features

*   **Speed PID Controller (Task 003a):**
    *   Closed-loop velocity control for the Nucleo.
    *   Tunable Kp, Ki, Kd parameters via serial.
    *   Documentation: `source/data/embeddedplatform/speedPid.rst`

*   **Steering & MPC Control (Task 003b):**
    *   Combined Speed + Steering command (`#mpc:speed;steer`).
    *   Anti-jitter logic for servo protection.
    *   Documentation: `source/data/embeddedplatform/steeringControl.rst`

*   **IPM Matrix Calculation (Task 002a):**
    *   Tools for calculating Inverse Perspective Mapping (Birds-eye view).
    *   Includes Mac/PC local development support.
    *   Documentation: `source/data/brain/ipm.rst`

*   **Video Stream Handler (Task 001b):**
    *   Bridges Gazebo/ROS images to OpenCV.
    *   Subscribes to `/camera/rgb/image_raw` and publishes preview.
    *   Documentation: `source/data/brain/videostream.rst`

*   **Lane Segmentation (Task 002b):**
    *   Isolates the track using ROI masking and thresholding.
    *   Publishes binary mask to `/raven/perception/lane_mask`.
    *   Documentation: `source/data/brain/lanesegmentation.rst`

*   **Lateral Control (Task 002c):**
    *   Calculates steering error from lane mask centroid.
    *   Publishes error to `/raven/control/lateral_error`.
    *   Documentation: `source/data/brain/lateralcontrol.rst`

*   **Python Serial Class (Task 005a):**
    *   Robust 10Hz serial sender for Nucleo communication.
    *   Thread-safe speed/steer updates and auto-reconnection.
    *   Documentation: `source/data/brain/seriallink.rst`

*   **Steer Angle Mapping (Task 005b):**
    *   Converts lateral error to physical steering angles.
    *   Documentation: `source/data/brain/steering_mapper.rst`

*   **FSM Framework & Sign Logic (Task 006a/b):**
    *   Central Brain managing states (Idle, Drive, Stop).
    *   Enforces traffic rules (Stop Sign logic).
    *   Documentation: `source/data/brain/fsm.rst`

*   **Telemetry Dashboard (Task 007a):**
    *   Mission Control UI for visualization and emergency stop.
    *   Documentation: `source/data/brain/dashboard.rst`

*   **Serial Lexer & Parser (Task 004a/b):**
    *   UART input scanning (#...;;) and Float extraction (sscanf).
    *   Documentation: `source/data/embeddedplatform/serial_lexer_parser.rst`

*   **Synthetic Data Generator (Task 008a):**
    *   Automated script in `raven-sim` to capture diverse training datasets.
    *   Documentation: `source/data/brain/synthetic_capture.rst`

*   **Sign Recognition AI (Task 008b):**
    *   YOLOv8-based detection of Stop Signs and Parking Meters.
    *   Documentation: `source/data/brain/sign_recognition.rst`

*   **Dead Man Switch (Task 004c):**
    *   Motor safety watchdog (500ms timeout) to prevent runaway vehicles.
    *   Documentation: `source/data/embeddedplatform/dead_man_switch.rst`

Updated on Mon 02 Feb, 2026 by Hatem Soliman
