Serial Message Lexer & Parser (004a/b)
======================================

The **Serial Communication Stack** on the Nucleo (Team B) is responsible for receiving, validating, and interpreting commands from the Brain (Team C).

Lexer (Task 004a)
-----------------
The Lexer scans the incoming UART stream for the protocol message boundaries.

*   **Start Delimiter**: ``#``
*   **End Delimiter**: ``;;\r\n``
*   **Key/Value Split**: Separates the Command ID (e.g., ``SPEED``) from the Payload using ``:``.
*   **Implementation**: ``drivers/serialmonitor.cpp`` handles the circular buffer and ISR-based character reception.

Command Parser (Task 004b)
--------------------------
The Parser interprets the extracted payload string into numerical values for the actuators.

*   **Mechanism**: Uses ``sscanf`` to convert ASCII strings to Integers/Floats.
*   **Callbacks**: A function map routes commands to their specific handlers (e.g., ``serialCallbackSPEEDcommand``).
*   **Validation**: Checks if the parsed values are within safe physical limits before applying them.
*   **Implementation**: ``brain/robotstatemachine.cpp`` and ``main.cpp``.

Supported Commands
------------------
*   ``#SPEED:val;;`` - Sets DC motor PWM/Velocity.
*   ``#STEER:val;;`` - Sets Servo Angle (degrees).
*   ``#BRAKE:angle;;`` - Engages braking logic.
