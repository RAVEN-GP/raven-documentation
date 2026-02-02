Lane Segmentation (002b)
========================

The **Lane Segmentation** node is responsible for identifying the drivable track within the camera's field of view. It processes the raw images from the video stream and produces a binary mask where the track is highlighted.

Purpose
-------
*   **Subscribe**: Listens to the raw camera feed ``/camera/rgb/image_raw``.
*   **Process**: Applies Region of Interest (ROI) masking and color thresholding.
*   **Output**: Publishes a binary mask (black and white image) to ``/raven/perception/lane_mask``.

Implementation Details
----------------------
*   **Repository**: ``raven-brain-stack``
*   **Path**: ``ros_packages/raven_vision/src/lane_segmentation.py``
*   **Technique**: Classical Computer Vision (OpenCV)
    1.  **ROI**: A trapezoidal mask focuses the vision on the road ahead, ignoring background distractions.
    2.  **Thresholding**: Isolate white pixels (track markings) from the rest of the environment.

Usage
-----
To run the node:

.. code-block:: bash

    # Source your workspace if needed
    source devel/setup.bash 
    
    # Run the python script
    python3 ros_packages/raven_vision/src/lane_segmentation.py

Topic Inputs/Outputs
--------------------
*   **Input**: ``/camera/rgb/image_raw`` (sensor_msgs/Image)
*   **Output**: ``/raven/perception/lane_mask`` (sensor_msgs/Image - Encoding: mono8)
