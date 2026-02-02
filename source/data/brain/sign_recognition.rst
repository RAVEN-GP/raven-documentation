Sign Recognition AI (008b)
==========================

The **Sign Recognition AI** module gives RAVEN the ability to semantically understand traffic signs using Deep Learning (YOLOv8).

Purpose
-------
*   **Detection**: Identifies specific objects in the camera frame (e.g., Stop Signs, Parking Signs).
*   **Classification**: Distinguishes between different sign types to trigger appropriate FSM behaviors.

Implementation Details
----------------------
*   **Repository**: ``raven-brain-stack``
*   **Path**: ``src/perception/sign_recognition/sign_detector.py``
*   **Model**: YOLOv8 Nano (``yolov8n.pt``)
*   **Framework**: Ultralytics YOLO + ROS

Topic Inputs/Outputs
--------------------
*   **Input**: ``/camera/image_raw`` (sensor_msgs/Image)
*   **Output**: ``/detected_sign`` (std_msgs/String) -- JSON format: ``{"detected_object": "stop_sign"}``

Logic
-----
1.  **Callback**: Receives camera frame.
2.  **Inference**: Runs YOLOv8 inference on the frame.
3.  **Filter**: Checks for specific classes: ``stop sign``, ``parking meter``.
4.  **Publish**: If detected, publishes the simplified label to the FSM.
