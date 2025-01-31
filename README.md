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
- **MANUAL:** The pilot's control inputs (raw user inputs from RC transmitter) are passed directly to servos. No sensor feedback is used.
- **STABILIZED:** The pilot directly commands the roll and pitch angle. Thrust is directly set by the pilot.

**Auto Modes**
- **STARTUP**: Estimator initialization and receives waypoints from radio. Once complete, the plane switches to TAKEOFF_DETECT mode.
- **TAKEOFF_DETECT**: If the acceleration is above LAUN_ACC_THLD for LAUN_ACC_TIME seconds, the plane switches to TAKEOFF mode.
- **TAKEOFF** After LAUN_MOT_DEL seconds has passed after takeoff is detected, the throttle is set to the TAKEOFF_THR and the plane holds a pitch angle TAKEOFF_PTCH. Once the altitude is above the takeoff altitude threshold TAKEOFF_ALT, the plane switches to cruise mode.
- **MISSION:** Waypoint following
- **LAND:**

**Navigation**
- **INITIALIZATION:** Wait for position and attitude estimates to align and stabalize
- **LIVE**: Full INS/GPS fusion 

# Controller Diagrams

# Parameters and Configurations

# System Startup

# Telemetry

## Telemetry Protocol

| Byte Index | Content      | Value |
| ---------- | ------------ | ----- |
| 0          | Start byte   | 0x0F  |
| 1          | Length       |       |
| 2          | Consistent Overhead Byte Shuffling | |
| 3          | Message type |       |
| 4          | Checksum     | 0xFF  |

## Command Protocol

| Byte Index | Content      | Value |
| ---------- | ------------ | ----- |
| 0          | Start byte   | 0x0F  |
| 1          | Length       |       |
| 2          | Consistent Overhead Byte Shuffling | |
| 3-30       | Payload      |       |
| 31         | Checksum     | 0xFF  |

## Message types

| Message type         | Value | Explanation |
| -------------------- | ----- | ----------- |
| Calibrate Gyroscopes | 101   |             |

## Loading Waypoints
Waypoints are loaded via radio
Transmit command for waypoint, then transmit waypoint number, then transmit waypoint.

# TODO
- Telemetry
- Switching between manual and auto flight state
- Use Python to read data log in micro-SD card
- Commands to calibrate barometer, gyroscope, etc.
- A place to store configuration
- Don't mix and match seconds and microseconds
