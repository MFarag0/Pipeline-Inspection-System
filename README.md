# Pipeline Inspection System

## Overview
This project takes the WonderPi MasterPI, a commercial robotics chassis, and repurposes it into a fully specialized, independently engineered robotic system. While the MasterPI platform provides a strong mechanical foundation a rigid chassis, a built-in Li-ion battery pack, and four mecanum wheels for omnidirectional movement, its stock control hat limits customization and flexibility over how the robot's subsystems are wired together and 
controlled. To overcome this, the stock hat was removed entirely and replaced with a Raspberry Pi 5, giving full control over every layer of the system: from low-level motor and servo signaling to networking and vision 
processing.

Rebuilding the electronics around the Pi 5 meant every subsystem had to be designed, wired, and powered from scratch, rather than relying on a pre-integrated board. This included selecting appropriate motor drivers for the 
four independent DC motors, choosing a dedicated PWM controller to drive the robotic arm's servos smoothly and without jitter, isolating power delivery across motors, servos, and the Pi to avoid brownouts, and integrating a USB camera for real-time vision tasks such as line following and color detection. On top of the hardware, a custom client-server software stack was developed so that the robot could be operated wirelessly and in real time from a separate computer, rather than requiring a screen and peripherals attached directly to the robot.

Development followed a hardware-first approach, with subsystems built and validated incrementally: motor control was implemented first to establish reliable movement, followed by servo actuation for the robotic arm, then 
camera integration for vision capabilities, and finally the user-interface, where two versions were created: an app built from Javascript, and an app built from Matlab App Designer, and wireless connectivity layer that 
ties everything together. The result is a robot that retains the mechanical robustness of a commercial platform while gaining the flexibility, precision, and extensibility of a fully custom-built control system.

<img width="934" height="664" alt="image" src="https://github.com/user-attachments/assets/35262f48-26ba-4640-85db-9ea5000a299b" />

## Hardware Stack

| Subsystem | Components | Notes |
|---|---|---|
| **Chassis** | WonderPi MasterPI base, mecanum wheels, built-in Li-ion battery pack | Provides mechanical foundation; stock control hat removed |
| **Compute** | Raspberry Pi 5 | Central controller for motors, servos, camera, and networking |
| **Drive Motors** | 4x DC motors (mecanum wheels), 2x L298N motor driver modules | Each L298N drives 2 motors; 8 GPIO pins for direction (2 per motor) + 1 PWM output for shared speed control; enables strafing, rotation, and diagonal motion |
| **Motor Power** | 2x 3.7V Li-ion cells in series (~6V), PWM-regulated | Powers motors directly, isolated from Pi's power rail to prevent brownouts |
| **Robotic Arm** | 5x servo motors, 16-channel PWM servo controller (I2C) | Servos wired to channels 0, 2, 4, 6, 8 for a clean claw-to-base mapping; controller communicates via SDA/SCL, offloading PWM generation from the Pi to eliminate jitter |
| **Servo Power** | Adjustable buck converter | Steps battery voltage down to a clean, isolated 5V rail for the servo controller and servos |
| **Pi Power** | Dedicated high-capacity power bank | Powers the Pi 5 via USB-C independently of the servo/motor rails, ensuring stable supply during compute-heavy tasks (e.g., vision processing) |
| **Vision** | USB camera | Direct USB connection to the Pi; used with OpenCV for line following, color detection, and general vision tasks |

## Software Stack

| Software | Role |
|---|---|
| **Python 3** | Server-side logic on the Raspberry Pi 5; handles GPIO/motor/servo control and networking (`asyncio` for the control server, HTTP for the camera stream) |
| **HTML, CSS, JavaScript** | Used to build a web interface to operate the robot |
| **MATLAB** | Client-side networking (`tcpclient` for TCP/IP control commands, `imread` for pulling camera frames over HTTP) |
| **MATLAB App Designer** | Builds the GUI (buttons, sliders, connection controls) used to operate the robot |
| **Raspberry Pi OS** | Host OS for all server-side code; provides GPIO/hardware access |

## How It Works
The system uses a client-server architecture:

1. Client (Web App / MATLAB GUI): Runs on a laptop/desktop. Every user action (e.g., pressing "Forward," adjusting a servo slider) is translated into a text command such as MOVE FWD or SERVO 2 90.
2. Control Server (Python, asyncio, TCP/IP): Running on the Pi, this server accepts the client's TCP connection, parses incoming text commands, and executes the corresponding motor or servo action in real time.
3. Camera Server (Python, async HTTP): A separate lightweight server compresses live camera frames to JPEG and serves them over HTTP. The MATLAB client periodically issues GET requests and decodes the frames for display.

Running movement/servo control and the camera feed as two independent servers lets the robot handle both simultaneously without one blocking the other — giving the user uninterrupted control and a live video feed at the 
same time.

## Summary
By combining the WonderPi MasterPI's mechanical base with a Raspberry Pi 5, dual motor drivers, a dedicated servo controller, isolated power regulation, and a Python/MATLAB client-server stack, this project transforms a 
commercial robotics kit into a fully custom platform capable of precise omnidirectional movement, smooth 5-DOF arm actuation, and real-time remote-controlled vision.
