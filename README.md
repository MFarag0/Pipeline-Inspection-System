## Pipeline Inspection System

# Overview
This project takes the WonderPi MasterPI, a commercial robotics chassis, and repurposes it into a fully specialized, independently engineered robotic system. While the MasterPI platform provides a strong mechanical foundation
— a rigid chassis, a built-in Li-ion battery pack, and four mecanum wheels for omnidirectional movement — its stock control hat limits customization and flexibility over how the robot's subsystems are wired together and 
controlled. To overcome this, the stock hat was removed entirely and replaced with a Raspberry Pi 5, giving full control over every layer of the system: from low-level motor and servo signaling to networking and vision 
processing.

Rebuilding the electronics around the Pi 5 meant every subsystem had to be designed, wired, and powered from scratch, rather than relying on a pre-integrated board. This included selecting appropriate motor drivers for the 
four independent DC motors, choosing a dedicated PWM controller to drive the robotic arm's servos smoothly and without jitter, isolating power delivery across motors, servos, and the Pi to avoid brownouts, and integrating a USB camera for real-time vision tasks such as line following and color detection. On top of the hardware, a custom client-server software stack was developed so that the robot could be operated wirelessly and in real time from a separate computer, rather than requiring a screen and peripherals attached directly to the robot.

Development followed a hardware-first approach, with subsystems built and validated incrementally: motor control was implemented first to establish reliable movement, followed by servo actuation for the robotic arm, then 
camera integration for vision capabilities, and finally the user-interface, where two versions were created: an app built from Javascript, and an app built from Matlab App Designer, and wireless connectivity layer that 
ties everything together. The result is a robot that retains the mechanical robustness of a commercial platform while gaining the flexibility, precision, and extensibility of a fully custom-built control system.

# Hardware Stack

<img width="621" height="689" alt="image" src="https://github.com/user-attachments/assets/b9a87918-15cb-4586-9586-70422dcdba84" />

# Software Stack

<img width="623" height="304" alt="image" src="https://github.com/user-attachments/assets/0ae38a83-5e38-4322-a726-5a315a6e854a" />

# How It Works
The system uses a client-server architecture:

1. Client (Web App / MATLAB GUI): Runs on a laptop/desktop. Every user action (e.g., pressing "Forward," adjusting a servo slider) is translated into a text command such as MOVE FWD or SERVO 2 90.
2. Control Server (Python, asyncio, TCP/IP): Running on the Pi, this server accepts the client's TCP connection, parses incoming text commands, and executes the corresponding motor or servo action in real time.
3. Camera Server (Python, async HTTP): A separate lightweight server compresses live camera frames to JPEG and serves them over HTTP. The MATLAB client periodically issues GET requests and decodes the frames for display.

Running movement/servo control and the camera feed as two independent servers lets the robot handle both simultaneously without one blocking the other — giving the user uninterrupted control and a live video feed at the 
same time.

# Summary
By combining the WonderPi MasterPI's mechanical base with a Raspberry Pi 5, dual motor drivers, a dedicated servo controller, isolated power regulation, and a Python/MATLAB client-server stack, this project transforms a 
commercial robotics kit into a fully custom platform capable of precise omnidirectional movement, smooth 5-DOF arm actuation, and real-time remote-controlled vision.
