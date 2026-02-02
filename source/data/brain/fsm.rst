FSM Framework & Logic (006a/b)
============================

The **Finite State Machine (FSM)** is the high-level brain of RAVEN. It determines the vehicle's mode of operation based on sensor inputs and user commands.

States
------
*   **IDLE**: Vehicle is stationary.
*   **DRIVE**: Standard autonomous lane-following.
*   **STOP_WAIT**: Temporary stop handling (e.g., Stop Sign).
*   **PARKING**: Managing the dedicated parking maneuver.

Sign Response Logic (Task 006b)
-------------------------------
The FSM listens to the AI output (``/raven/perception/sign``) and enforces traffic rules:

*   **STOP_SIGN**: Transition to ``STOP_WAIT`` -> Set Speed 0.0 -> Wait 3 Seconds -> Resume ``DRIVE``.
*   **PARKING_SIGN**: Transition to ``PARKING``.

Implementation Details
----------------------
*   **Repository**: ``raven-brain-stack``
*   **Package**: ``raven_control``
*   **Path**: ``ros_packages/raven_control/src/raven_fsm.py``

Topic Inputs/Outputs
--------------------
*   **Input**: ``/raven/perception/sign`` (String), ``/raven/dashboard/command`` (String)
*   **Output**: ``/raven/control/target_speed`` (Float32), ``/raven/fsm/state`` (String)
