IPM Matrix Calculation
======================

.. note::
   This feature is available in the `002a-ipm-matrix-calc` branch of the Brain project.

Overview
--------
Inverse Perspective Mapping (IPM) transforms the forward-facing camera view into a top-down "Bird's-Eye View". This is essential for:

- Accurate distance measurement (pixels to cm).
- Lane detection and maintaining lane position.
- Object localization on the ground plane.

Local Development (Mac/PC)
--------------------------
To test the calibration tools without a Raspberry Pi:

1. Initialize the environment:

   .. code-block:: bash

       python3 -m venv venv
       source venv/bin/activate
       pip install -r requirements-mac.txt

2. The ``collect_images.py`` script will automatically fall back to your webcam if the Pi Camera is not found.

Workflow
--------
The calibration process is divided into three steps, utilizing scripts found in `src/calibration/`.

1. Data Collection
~~~~~~~~~~~~~~~~~~
Capture images of a checkerboard (8x6 squares recommended).

.. code-block:: bash

   python3 calibration/collect_images.py

- Press **'s'** to save frames.
- Capture multiple angles for intrinsic calibration.
- Capture **one final image** with the checkerboard flat on the floor (lane view) for the perspective transform.

2. Calibration
~~~~~~~~~~~~~~
Calculate the camera matrix and homography matrix.

.. code-block:: bash

   python3 calibration/calibrate_ipm.py --perspective_img calibration/images/calib_lane_view.jpg

- The script first performs **intrinsic calibration** using the collected frames.
- It then opens a UI for **extrinsic calibration**.
- Click the **4 corners** of the checkerboard (or a known rectangle) on the floor in **Clockwise Order** (Top-Left -> Top-Right -> Bottom-Right -> Bottom-Left).
- Results are saved to ``calibration/data/calib_data.json``.

3. Verification
~~~~~~~~~~~~~~~
Verify that parallel lines on the road appear parallel in the warped image.

.. code-block:: bash

   python3 calibration/verify_ipm.py calibration/images/test_image.jpg

Output
------
The tool generates a JSON file (`calib_data.json`) containing:

- ``camera_matrix``: Intrinsic parameters of the Raspberry Pi Camera.
- ``dist_coeffs``: Distortion coefficients.
- ``homography_matrix``: The 3x3 matrix used to warp perspective images to top-down view.

Usage in Code
-------------
To use the matrix in your Python code:

.. code-block:: python

   from calibration.utils import load_calibration_data, apply_ipm

   # Load once
   mtx, dist, homography = load_calibration_data()

   # Apply to every frame
   warped_frame = apply_ipm(current_frame, homography)
