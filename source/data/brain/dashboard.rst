Telemetry Dashboard (007a)
==========================

The **Telemetry Dashboard** is the mission control UI run on the Host PC (`raven-computer`). It provides real-time visualization and emergency control of the vehicle.

Features
--------
*   **Real-time Data**: Visualizes Speed, Steering Angle, and Battery Life via WebSockets.
*   **Mission Control**: Start/Stop buttons to command the FSM remotely.
*   **Emergency Stop**: High-priority cutoff switch.

Implementation Details
----------------------
*   **Repository**: ``raven-computer``
*   **Tech Stack**: Python (Flask-SocketIO), HTML/JS
*   **Path**: ``src/dashboard/app.py``

Usage
-----
.. code-block:: bash

    # On the Computer
    cd src/dashboard
    python3 app.py
    # Open browser to http://localhost:5000
