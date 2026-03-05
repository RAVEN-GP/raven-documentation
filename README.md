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

---

## 🔌 Hardware Connection & Flashing Workflow

The RAVEN platform utilizes a Raspberry Pi (High-Level Brain) connected to an Arduino Nano RP2040 Connect (Low-Level Controller). Depending on your development stage, the hardware connection flow changes:

### Option 1: Development Mode (Desk Testing)
*You are writing code on your Mac.*
1. **Connection:** Plug the **Arduino** directly into your **Mac** via USB.
2. **Flashing:** Run `raven flash --arch arduino` from your Mac terminal to compile and upload the `raven-rp2040.ino` firmware directly.
3. **Execution:** Run `python3 serial_controller.py` on your Mac to test motors and sensors.

### Option 2: Production Mode (Driving on Track)
*The car is fully assembled.*
1. **Connection:** The **Arduino** is physically mounted and plugged into the **Raspberry Pi** via USB. Both are powered by the car's battery.
2. **Access:** Connect your Mac to the Raspberry Pi over Wi-Fi (SSH).
3. **Flashing:** To update firmware, SSH into the Pi, run `git pull`, and execute `raven flash --arch arduino` **on the Pi**. The Pi acts as the compilation and flashing host.
4. **Execution:** The `serial_controller.py` script runs on the Pi, handling the real-time control loop autonomously.

---


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
    *   YOLOv8-based detection of 9 BFMC miniature traffic signs.
    *   **Training Pipeline:**
        *   Run 1: 100 epochs on Bosch Traffic Signs dataset (561 images, 9 classes).
        *   Run 3: +10 fine-tuning epochs with negative mining (39 red shirt backgrounds).
    *   **Negative Mining:** Empty label files for non-sign images teach the model to reject false positives (e.g., red shirts misidentified as stop signs).
    *   **Production Models (in `src/perception/sign_recognition/`):**
        *   `bfmc_best_shirts.pt` — ⭐ Recommended (mAP50: 93.3%, shirt-aware).
        *   `bfmc_best.pt` — Base model (mAP50: 92.7%).
        *   `bfmc_last_shirts.pt` / `last.pt` — Checkpoints for resuming training.
    *   **Training Scripts (in `training_workspace/`):**
        *   `train_signs.py` — Fresh training from `yolov8n.pt`.
        *   `resume_training.py` — Resume interrupted training.
        *   `finetune_shirts.py` — Fine-tune with negative mining data.
    *   **Post-Detection Filters (`sign_filters.py`):**
        *   Shape validation (aspect ratio rejects car-shaped false positives).
        *   HSV color validation (lighting-independent hue check).
        *   Size filter (rejects tiny noise and frame-filling detections).
    *   Documentation: `source/data/brain/sign_recognition.rst`

*   **Dead Man Switch (Task 004c):**
    *   Motor safety watchdog (500ms timeout) to prevent runaway vehicles.
    *   Documentation: `source/data/embeddedplatform/dead_man_switch.rst`

*   **Remote Control System (Task 007b):**
    *   Manual control via Web Dashboard (WASD).
    *   Arduino RP2040 firmware with H-Bridge support.
    *   Documentation: `source/data/remote_control.rst`

Updated on Mon 02 Feb, 2026 by Hatem Soliman
