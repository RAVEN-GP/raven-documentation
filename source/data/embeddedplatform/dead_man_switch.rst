Dead Man Switch (004c)
======================

The **Dead Man Switch** is a critical safety feature implemented on the Nucleo (Team B). It acts as a watchdog timer to prevent "runaway vehicle" scenarios if the connection to the Brain (Raspberry Pi) is lost.

Purpose
-------
*   **Safety**: Forces the vehicle to stop if no valid commands are received within a safety timeout.
*   **Fail-safe**: ensures the car doesn't keep executing the last command (e.g., "Full Speed") endlessly if the serial cable disconnects or the Python script crashes.

Implementation Details
----------------------
*   **Repository**: ``raven-embedded-control``
*   **Files**: 
    *   ``include/utils/deadmanswitch.hpp``
    *   ``source/utils/deadmanswitch.cpp``
*   **Integration**: Used in ``brain/robotstatemachine.cpp``.

Logic
-----
1.  **Timeout**: Configured to **500ms**.
2.  **Reset**: Every valid command (SPEED/STEER/BRAKE) received via Serial resets the internal timer.
3.  **Trigger**: If 500ms elapses without a reset, the ``applySafeSpeed`` wrapper forces the motor speed to **0**.

Usage in Code
-------------
.. code-block:: cpp

    // In robotstatemachine.cpp
    m_deadManSwitch.resetWatchdog(); // Called on valid serial parsing
    
    // ... later in the run loop ...
    m_deadManSwitch.applySafeSpeed(m_speed); // Passes 0 if timed out, else passes m_speed
