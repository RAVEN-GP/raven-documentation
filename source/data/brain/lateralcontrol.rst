Lateral Control (002c)
======================

The **Lateral Control** node calculates the steering error based on the lane segmentation mask. It determines how far the vehicle is from the center of the lane and publishes this error for the PID controller.

Purpose
-------
*   **Subscribe**: Listens to the binary lane mask ``/raven/perception/lane_mask``.
*   **Calculate**: Computes the centroid of the visible lane markings.
*   **Output**: Publishes a float error value to ``/raven/control/lateral_error``.

Implementation Details
----------------------
*   **Repository**: ``raven-brain-stack``
*   **Path**: ``ros_packages/raven_vision/src/lateral_control.py``
*   **Logic**:
    1.  **Slicing**: Takes the bottom 20% of the lane mask to focus on the immediate path.
    2.  **Moments**: Uses ``cv2.moments`` to find the center (centroid) of the white pixels.
    3.  **Error Calculation**: ``Image Center X - Centroid X``.
        *   Positive Error: Car is to the LEFT (Steer Right).
        *   Negative Error: Car is to the RIGHT (Steer Left).

Usage
-----
To run the node:

.. code-block:: bash

    # Source your workspace if needed
    source devel/setup.bash 
    
    # Run the python script
    python3 ros_packages/raven_vision/src/lateral_control.py

Topic Inputs/Outputs
--------------------
*   **Input**: ``/raven/perception/lane_mask`` (sensor_msgs/Image - mono8)
*   **Output**: ``/raven/control/lateral_error`` (std_msgs/Float32)
