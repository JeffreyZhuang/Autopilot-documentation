# Autopilot Documentation

<p float="left">
  <img src="images/enclosure.webp" width="45%"/>
  <img src="images/mainboard.webp" width="45%"/>
</p>

# Coordinate System

<img src="images/coordinate-system.png" width="45%"/>

The Kalman filter calculates the position in meters in reference to the position of the first GPS fix.

# Software Architecture

## Update Rates

The main loop and PID controllers run at 100Hz. The servo library limits the pwm signal update rate to the maximum allowed by the servos (50Hz).

# State Machine

**System Modes**
- **BOOT:** Calibrate barometer and set home position. Once boot is complete, the transmitter is in a safe state, and the vehicle has been armed, the system mode switches to flight.
- **FLIGHT:** Execute manual and auto modes

**Manual Modes**
- **MANUAL:** The pilot's control inputs (raw user inputs from RC transmitter) are passed directly to servos. No sensor feedback is used.
- **STABILIZED:** The pilot directly commands the roll and pitch angle. Thrust is directly set by the pilot.

**Auto Modes**
- **STARTUP**: Estimator initialization and receives waypoints from radio. The home lat/lon reference is set to the position of the first GPS fix. Once complete, the plane switches to TAKEOFF_DETECT mode.
- **TAKEOFF_DETECT**: If the acceleration is above LAUN_ACC_THLD for LAUN_ACC_TIME seconds, the plane switches to TAKEOFF mode.
- **TAKEOFF** After LAUN_MOT_DEL seconds has passed after takeoff is detected, the throttle is set to the TAKEOFF_THR and the plane holds a pitch angle TAKEOFF_PTCH. Once the altitude is above the takeoff altitude threshold TAKEOFF_ALT, the plane switches to MISSION mode.
- **MISSION:** Waypoint following. When passed final waypoint, the plane siwtches to LAND mode.
- **LAND:** Follows a 10 degree glideslope to the landing point
- **FLARE:**
- **TOUCHDOWN:**

# Controller Diagrams

# Parameters and Configurations

# System Startup

# Radio Communications Link

## Transport Protocol Layer

Fixed packet size of 40 bytes and a payload of 38 bytes, therefore no length byte is needed.

| Byte Index | Content                            | Type    |
| ---------- | ---------------------------------- | ------- |
| 0          | Start byte (0x00)                  | uint8_t |
| 1          | Consistent Overhead Byte Shuffling | uint8_t |
| 2-39       | Payload                            |         |

## Interface layer

The following paylods are injected into the "payloads" section of the transport protocol layer.

### Telemetry Payload

| Byte Index | Content          | Type    |
| ---------- | ---------------- | ------- |
| 0          | Payload Type (0) | uint8_t |
| 1-4        | Roll             | float   |
| 5-8        | Pitch            | float   |
| 9-12       | Heading          | float   |
| 13-16      | Altitude         | float   |
| 17-20      | Speed            | float   |
| 21-24      | Latitude         | float   |
| 25-28      | Longitude        | float   |
| 29-37      | Empty            |         |

### Command Payload

| Byte Index | Content          | Type    |
| ---------- | ---------------- | ------- |
| 0          | Payload Type (1) | uint8_t |
| 1          | Command ID       | uint8_t |
| 2-37       | Empty            |         |

### Waypoint Payload

| Byte Index | Content          | Type    |
| ---------- | -----------------| ------- |
| 0          | Payload Type (2) | uint8_t |
| 1          | Waypoint Index   | uint8_t |
| 2-5        | Position North   | float   |
| 6-9        | Position East    | float   |
| 10-13      | Position Down    | float   |
| 14-37      | Empty            |         |

### Payload Types

| Payload Type | Value |
| ------------ | ----- |
| Telemetry    | 0     |
| Command      | 1     |
| Waypoint     | 2     |

### Command ID List

| Command              | Command ID Value |
| -------------------- | ---------------- |
| Calibrate Gyroscopes | 0                |
| Calibrate Barometer  | 1                |

# TODO
- Telemetry
- Switching between manual and auto flight state
- Use Python to read data log in micro-SD card
- Commands to calibrate barometer, gyroscope, etc.
- A place to store configuration
- Don't mix and match seconds and microseconds
