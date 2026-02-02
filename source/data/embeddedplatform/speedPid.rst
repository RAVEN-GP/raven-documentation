Speed PID Controller
====================

.. note::
   This feature is available in the `003a-speed-pid-controller` branch of the embedded project.

Overview
--------
The Speed PID Controller provides closed-loop velocity control for the vehicle. Unlike the default open-loop PWM control, this module actively adjusts motor power to maintain a target speed (in mm/s) regardless of battery voltage drop, incline, or friction.

Key Features
~~~~~~~~~~~~
- **Closed-Loop Control**: Implemented using IMU-based feedback from the BNO055 sensor.
- **Serial-Configurable Gains**: Tune PID gains (Kp, Ki, Kd) at runtime via serial commands (no reflashing needed!).
- **Anti-Windup Protection**: Integral term clamping prevents saturation during stops/starts.
- **Derivative Low-Pass Filter**: Includes an optional EMA filter to reduce IMU noise amplification.
- **Feedforward Compensation**: Uses target speed as a base for faster setpoint response.
- **Global Velocity Variables**: Exposed IMU velocity data for PID consumption.

 Architecture
------------
The controller is implemented as a periodic task (`CSpeedPIDController`) running at **100Hz (10ms period)**. It integrates with the existing system by:

1.  Reading global velocity variables (updated by `CImu`).
2.  Computing the control signal (PID + Feedforward).
3.  Sending the adjusted PWM to the `CSpeedingMotor` driver.

Serial Commands
---------------
You can control and tune the PID controller via the serial interface (using PuTTY or the Brain project).

.. list-table:: Available Commands
   :widths: 20 40 40
   :header-rows: 1

   * - Command
     - Format
     - Description
   * - ``pidGains``
     - ``#pidGains:<Kp>;<Ki>;<Kd>;;\r\n``
     - Set PID gains (×100 scale). E.g., `300`=3.0.
   * - ``pidTarget``
     - ``#pidTarget:<mm/s>;;\r\n``
     - Set the target velocity in mm/s.
   * - ``pidEnable``
     - ``#pidEnable:<0|1>;;\r\n``
     - Enable (1) or Disable (0) the PID loop.
   * - ``pidStatus``
     - ``#pidStatus:0;;\r\n``
     - Query current controller state (Enabled, Target, Error, Gains).

Tuning Guide
------------
The default values are tuned for the standard Reely TC-04 chassis with a 2S LiPo battery.

1.  **Kp (Proportional):** Increase until the car oscillates slightly around the target speed, then reduce. Default: **3.0** (Sending `300`).
2.  **Ki (Integral):** Increase to remove steady-state error (e.g., if car is slower than target). Too much causes instability. Default: **0.3** (Sending `30`).
3.  **Kd (Derivative):** Use sparingly to dampen oscillations. Default: **0.05** (Sending `5`).

Files Modified
--------------
This feature touched several key areas of the embedded platform:

- `include/periodics/speedpidcontroller.hpp` (New): PID controller header.
- `source/periodics/speedpidcontroller.cpp` (New): PID implementation.
- `include/brain/globalsv.hpp` & `source/brain/globalsv.cpp`: Added global velocity variables.
- `source/periodics/imu.cpp`: Exported velocity to global variables.
- `source/main.cpp`: Registered task and serial callbacks.
