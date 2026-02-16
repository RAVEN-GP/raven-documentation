Remote Control System
=====================

This guide documents the Remote Control System implemented for the RAVEN platform, enabling manual control of the vehicle via a web dashboard.

Architecture
------------

The system follows a 3-tier architecture:

1.  **Dashboard (raven-computer)**:
    -   Located in ``raven-computer/src/dashboard``.
    -   A Flask-based web interface ("Neon Green UI").
    -   Captures keyboard input (WASD/Arrows) and sends SocketIO messages.
    -   Connects to the Brain on Port 5005.

2.  **Brain (raven-brain-stack)**:
    -   Located in ``raven-brain-stack``.
    -   Hosted on the Raspberry Pi (or simulation PC).
    -   **processDashboard**: Receives SocketIO commands (e.g., ``SpeedMotor:30``).
    -   **processSerialHandler**: Forwards command to the Embedded board via USB Serial.

3.  **Embedded (raven-embedded-control)**:
    -   Code: ``raven-embedded-control/arduino/raven-rp2040/raven-rp2040.ino``.
    -   Hardware: Arduino Nano RP2040 Connect.
    -   Receives Serial commands (``#speed:30;;``) and drives the motors.

Hardware Configuration
----------------------

The embedded control logic has been ported to **Arduino Nano RP2040 Connect** with the following pinout:

**Motor Driver (L298N / H-Bridge):**

*   **IN1**: Pin 7
*   **IN2**: Pin 8
*   **EN (PWM)**: Pin 9

**Steering Servo:**

*   **Signal**: Pin 11

Running the System
------------------

**1. Flash the Firmware:**

.. code-block:: bash

    raven flash --arch arduino

**2. Start the Stack:**

.. code-block:: bash

    raven start manual

**3. Open Dashboard:**
Navigate to ``http://localhost:5000`` in your browser.

Troubleshooting
---------------
*   **Car not moving?** Check battery voltage. If the battery is low, the USB connection may drop when motors engage.
*   **No Connection?** Ensure the dashboard shows "ONLINE". If not, check if ``raven-brain-stack`` is running on port 5005.
*   **Wi-Fi Control Issues?**
    *   Connect USB and open Serial Monitor (115200 baud).
    *   Reset the Arduino.
    *   Look for ``# IP: 192.168.x.x``. If IP is 0.0.0.0, Wi-Fi failed.
    *   Verify your computer/Raspberry Pi can ping that IP.
