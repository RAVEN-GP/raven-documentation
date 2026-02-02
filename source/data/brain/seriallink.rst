Python Serial Class (005a)
==========================

The **Python Serial Class** is the "Mouth" of the Brain project. It establishes and maintains the USB communication link with the Nucleo board (the "Spine"), ensuring a reliable flow of commands.

Purpose
-------
*   **Connect**: Managing the USB Serial connection (e.g., ``/dev/ttyACM0``), handling auto-reconnection on failure.
*   **Schedule**: Running a background thread to maintain a consistent transmission frequency (default 10Hz).
*   **Package**: Formatting raw data (speed, steer) into the strict string protocol required by the firmware.

Implementation Details
----------------------
*   **Repository**: ``raven-brain-stack``
*   **Path**: ``src/hardware/serialhandler/serial_link.py``
*   **Class Name**: ``SerialLink``

Features
--------
1.  **Thread-Safe**: Uses ``threading.Lock`` to allow main threads to update speed/steer safely while the background thread reads them.
2.  **Robust Loop**: Calculates the exact sleep time required to maintain 10Hz, compensating for transmission delays.
3.  **Auto-Reconnect**: If the USB cable is unplugged or the port errors out, it attempts to reconnect automatically every second.

Usage
-----
.. code-block:: python

    from src.hardware.serialhandler.serial_link import SerialLink
    
    # Initialize and Start
    link = SerialLink(port='/dev/ttyACM0', frequency=10.0)
    link.start()
    
    # Update command (thread-safe)
    link.set_drive_command(speed=15.0, steer=2.5)
    
    # ... application runs ...
    
    # Stop cleanly
    link.stop()

Protocol Format
---------------
The class sends strings in the format:
``#SPEED:15.00;STEER:2.50;;\r\n``
