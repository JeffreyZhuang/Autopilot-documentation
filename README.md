# Autopilot Documentation

<p float="left">
  <img src="images/enclosure.webp" width="45%"/>
  <img src="images/mainboard.webp" width="45%"/>
</p>

# Coordinate System

<img src="images/coordinate-system.png" width="45%"/>

# Software Architecture

## Update Rates

The main loop and PID controllers run at 100Hz. The servo library limits the pwm signal update rate to the maximum allowed by the servos (50Hz).

# State Machine

**Manual Modes**
- **Manual:** The pilot's control inputs (raw user inputs from RC transmitter) are passed directly to servos. No sensor feedback is used.
- **Stabilized:** The pilot directly commands the roll and pitch angle. Thrust is directly set by the pilot.

**Auto Modes**
- **Takeoff:** The throttle is set to the takeoff thrust and the plane holds a 10 degree pitch angle. Once the altitude is above the takeoff altitude threshold, the plane switches to cruise mode.
- **Cruise:** Waypoint following
- **Land:**

**Navigation**
- **Estimator Initilization:** Wait for position and attitude estimates to align and stabalize

# Controller Diagrams

# Parameters and Configurations

# System Startup

# Telemetry

# TODO
- Telemetry
- Switching between manual and auto flight state
- Use Python to read data log in micro-SD card
- Commands to calibrate barometer, gyroscope, etc.
- A place to store configuration
- Don't mix and match seconds and microseconds
