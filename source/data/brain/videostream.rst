Video Stream Handler (001b)
===========================

The **Video Stream Handler** acts as the "Eyes" of the RAVEN platform within the ROS1 Noetic stack. It bridges the gap between the BFMC Gazebo simulation and the computer vision algorithms (OpenCV).

Purpose
-------
*   **Subscribe**: Listens to the Gazebo camera topic ``/camera/rgb/image_raw``.
*   **Convert**: Transforms ROS ``sensor_msgs/Image`` messages into OpenCV ``numpy.ndarray`` (BGR8) format.
*   **Enable**: Provides the raw image data required for downstream tasks like Lane Segmentation (Team A) and Object Detection.

Implementation Details
----------------------
*   **Repository**: ``raven-brain-stack``
*   **Path**: ``ros_packages/raven_vision/src/video_stream_handler.py``
*   **ROS Package**: ``raven_vision``

Usage
-----
To run the node (ensure ``roscore`` and Gazebo are running):

.. code-block:: bash

    # Source your workspace if needed
    source devel/setup.bash 
    
    # Run the python script directly or via rosrun
    python3 ros_packages/raven_vision/src/video_stream_handler.py

Dependencies
------------
*   **ROS Noetic**: ``rospy``, ``sensor_msgs``, ``cv_bridge``
*   **Python Libraries**: ``opencv-python``, ``numpy``
