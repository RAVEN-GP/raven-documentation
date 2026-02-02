Synthetic Data Generator (008a)
===============================

The **Synthetic Data Generator** automates the collection of labeled training data from the Gazebo simulation. It moves the car to various positions/angles around traffic signs and captures screenshots, solving the "Data Scarcity" problem for Team A.

Purpose
-------
*   **Automation**: replaces manual screenshotting.
*   **Coverage**: Ensures diverse viewing angles (-60 to +60 degrees) and heights.
*   **Structured Output**: Saves images into folders labeled by sign type (e.g., ``dataset/STOP/STOP_A_0001.png``).

Implementation Details
----------------------
*   **Repository**: ``raven-sim``
*   **Path**: ``src/utils/scripts/synthetic_capture.py``
*   **Dependencies**: ``gazebo_msgs``, ``cv_bridge``, ``rospy``.

Usage
-----
Launch Gazebo first, then run:

.. code-block:: bash

    rosrun utils synthetic_capture.py --output_dir ~/training_data --shots_per_pose 2

Arguments
---------
*   ``--radius``: Distance from the sign (meters).
*   ``--angles_deg``: List of angles to capture (e.g., "-60,-30,0,30,60").
*   ``--target_signs``: Automatically finds STOP and PARKING signs in the world.
