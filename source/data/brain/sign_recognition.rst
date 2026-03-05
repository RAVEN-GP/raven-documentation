Sign Recognition AI (008b)
==========================

The **Sign Recognition AI** module gives RAVEN the ability to detect and classify 9 European miniature traffic signs using Deep Learning (YOLOv8), trained specifically for the Bosch Future Mobility Challenge track.

Detected Signs
--------------
*   🛑 **Stop** — Red octagon
*   🅿️ **Parking** — Blue square, white P
*   ⬧ **Priority** — Yellow diamond
*   🚶 **Crosswalk** — Blue square, pedestrian figure
*   🛣️ **Highway Entrance** — Green rectangle, motorway symbol
*   🚫 **Highway Exit** — Green rectangle + red slash
*   🔄 **Roundabout** — Blue circle, circular arrows
*   ⬆️ **One-way** — Blue square, upward arrow
*   ⛔ **No-entry** — Red circle, white horizontal bar

Implementation Details
----------------------
*   **Repository**: ``raven-brain-stack``
*   **Live Detector**: ``src/perception/sign_recognition/live_sign_detector.py`` (standalone, no ROS)
*   **Threaded Detector**: ``src/perception/sign_recognition/threads/threadSignDetection.py`` (RAVEN message bus)
*   **Legacy ROS Detector**: ``src/perception/sign_recognition/sign_detector.py``
*   **Framework**: Ultralytics YOLOv8 + OpenCV

Model Files
-----------
All production weights are stored in ``src/perception/sign_recognition/``:

.. list-table::
   :header-rows: 1
   :widths: 30 50 20

   * - File
     - Description
     - Accuracy
   * - ``bfmc_best_shirts.pt``
     - ⭐ **RECOMMENDED.** Fine-tuned with negative mining (red shirt rejection). Reduces false positives on red clothing.
     - mAP50: 93.3%
   * - ``bfmc_best.pt``
     - Base production model. 100 epochs on Bosch Traffic Signs dataset (561 images, 9 classes).
     - mAP50: 92.7%
   * - ``bfmc_last_shirts.pt``
     - Last checkpoint from shirt fine-tuning run (epoch 10/10). Use for resuming training.
     - —
   * - ``last.pt``
     - Last checkpoint from base 100-epoch training. Use for resuming training.
     - —

Per-Class Accuracy (bfmc_best_shirts.pt)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1
   :widths: 30 20

   * - Sign Class
     - mAP50-95
   * - Stop
     - 94.0%
   * - Priority
     - 92.0%
   * - Parking
     - 89.5%
   * - No Entry
     - 88.2%
   * - Crosswalk
     - 83.2%
   * - One Way
     - 81.6%
   * - Highway Exit
     - 80.2%
   * - Highway Entrance
     - 66.0%

Training Pipeline
-----------------

**Run 1 — Base Training (100 epochs)**

*   **Script**: ``training_workspace/train_signs.py``
*   **Base model**: ``yolov8n.pt`` (COCO pretrained, auto-downloaded)
*   **Dataset**: Bosch Traffic Signs YOLOv8 (Roboflow export, 561 images, 9 classes)
*   **Config**: ``training_workspace/Bosch Traffic Signs YOLOv8/data.yaml``
*   **Device**: Apple M2 GPU (MPS) / batch=16 / imgsz=640
*   **Output**: ``runs/detect/bfmc_models/sign_detector/``

**Run 3 — Negative Mining Fine-tune (+10 epochs)**

*   **Script**: ``training_workspace/finetune_shirts.py``
*   **Base model**: ``bfmc_best.pt`` (Run 1 output)
*   **Dataset**: Same as Run 1 + 39 red shirt images with empty label files
*   **Purpose**: Teach the model that red shirts ≠ stop signs
*   **Output**: ``runs/detect/bfmc_models/sign_detector_shirts/``

What is Negative Mining?
~~~~~~~~~~~~~~~~~~~~~~~~
The 39 shirt images have **empty** ``.txt`` label files (0 bytes). In YOLO, an image with an empty label file means "this image exists but contains **no objects**." During training, when the model tries to detect a stop sign in a red shirt image, the empty label corrects it. Over many iterations, the model learns to distinguish between actual signs and visually similar non-sign objects (like red clothing).

Training Scripts
~~~~~~~~~~~~~~~~
Located in ``training_workspace/``:

*   ``train_signs.py`` — Start a fresh 100-epoch training run from ``yolov8n.pt``
*   ``resume_training.py`` — Resume an interrupted training run from a ``last.pt`` checkpoint
*   ``finetune_shirts.py`` — Fine-tune an existing model with the shirt negative mining dataset
*   ``fix_yaml.py`` — Fix absolute paths in ``data.yaml`` for your local machine

How to Retrain
~~~~~~~~~~~~~~

.. code-block:: bash

   cd training_workspace
   source venv/bin/activate       # Activate Python venv
   python fix_yaml.py             # Fix paths for your machine

   # Option A: Fresh training (100 epochs)
   python train_signs.py

   # Option B: Resume interrupted training
   python resume_training.py

   # Option C: Fine-tune with negative mining
   python finetune_shirts.py

Usage
-----

**Live detection (standalone, no ROS):**

.. code-block:: bash

   # Pi Camera (default)
   python3 src/perception/sign_recognition/live_sign_detector.py \
       --model src/perception/sign_recognition/bfmc_best_shirts.pt

   # Webcam (Mac/PC)
   python3 src/perception/sign_recognition/live_sign_detector.py \
       --model src/perception/sign_recognition/bfmc_best_shirts.pt --webcam

**Controls:**

*   ``q`` — Quit
*   ``s`` — Save screenshot
*   ``p`` — Pause / Resume

**CLI Options:**

*   ``--model PATH`` — Path to YOLO ``.pt`` model (default: ``yolov8n.pt``)
*   ``--webcam`` — Force webcam instead of Pi Camera
*   ``--source PATH`` — Video file or image for offline testing
*   ``--conf FLOAT`` — Confidence threshold, 0.0–1.0 (default: 0.5)

Topic Inputs/Outputs (Threaded Mode)
------------------------------------
When running via the RAVEN message bus (``threadSignDetection.py``):

*   **Input**: ``mainCamera`` message (base64-encoded JPEG or numpy array)
*   **Output**: ``SignDetected`` message — JSON format: ``{"sign": "stop", "confidence": 0.95, "bbox": [x1, y1, x2, y2]}``
*   **Rate**: 5 Hz inference (configurable via ``inference_interval``)

The threaded detector automatically searches for model files in priority order:
``bfmc_best_shirts.pt`` → ``bfmc_best.pt`` → ``models/best.pt`` → ``last.pt`` → ``yolov8n.pt`` (fallback).
