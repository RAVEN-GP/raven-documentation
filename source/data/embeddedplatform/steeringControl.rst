Steering & MPC Control
======================

.. note::
   This feature is available in the `003b-steering-pid-controller` branch of the embedded project.

Overview
--------
This module introduces a high-level command interface (MPC) for controlling both speed and steering simultaneously. It also includes an "Anti-Jitter" mechanism to smooth out steering commands, preventing sudden mechanical stress on the steering servo.

Key Features
~~~~~~~~~~~~
- **Combined Command**: Set speed and steering angle in a single serial message.
- **Anti-Jitter**: Limits the maximum change in steering angle per command (`MAX_STEER_STEP`) to ensure smooth transitions.
- **Direct Control**: Bypasses some state machine timings for immediate response (while still respecting global limits).
- **Safety Checks**: Requires KL30 (high voltage) to be enabled before executing motor commands.

Serial Commands
---------------
The new `mpc` command allows external controllers (like the Brain) to send driving instructions.

.. list-table:: Available Commands
   :widths: 20 40 40
   :header-rows: 1

   * - Command
     - Format
     - Description
   * - ``mpc (Steer only)``
     - ``#mpc:<STEER>;;\r\n``
     - Set steering angle only. Speed remains 0.
   * - ``mpc (Speed & Steer)``
     - ``#mpc:<SPEED>;<STEER>;;\r\n``
     - Set both speed (mm/s) and steering angle.

**Parameters:**
- **SPEED**: Target velocity in mm/s (same units as `speedPid`).
- **STEER**: Steering angle (e.g., 120 = straight, depends on calibration).

Anti-Jitter Logic
-----------------
To protect the steering mechanics, the controller limits how fast the steering angle can change between updates.

- **MAX_STEER_STEP**: Defined as `12` (tunable in code).
- If the requested steering angle differs from the previous one by more than this step, the controller moves the servo incrementally towards the target rather than jumping immediately.

Files Modified
--------------
- `include/brain/robotstatemachine.hpp`: Added `serialCallbackMPCcommand` and anti-jitter state variables.
- `source/brain/robotstatemachine.cpp`: Implemented the command parsing and smoothing logic.
- `source/main.cpp`: Registered the `mpc` serial callback.
