Steer Angle Mapping (005b)
==========================

The **Steer Angle Mapping** node translates the raw centimeter error (from computer vision) into a physical steering angle (in degrees) that the Nucleo can execute.

Purpose
-------
*   **Controller**: Implements a PD (Proportional-Derivative) controller to smooth out the steering and prevent oscillation.
*   **Safety**: Clamps the output angle to the physical limits of the vehicle geometry (-25 to +25 degrees).

Implementation Details
----------------------
*   **Repository**: ``raven-brain-stack``
*   **Package**: ``raven_control``
*   **Path**: ``ros_packages/raven_control/src/steering_mapper.py``

Parameters
----------
*   ``Kp`` (default 0.8): Proportional gain.
*   ``Kd`` (default 0.1): Derivative gain (damping).

Topic Inputs/Outputs
--------------------
*   **Input**: ``/raven/control/lateral_error`` (Float32)
*   **Output**: ``/raven/control/steer_angle`` (Float32)
