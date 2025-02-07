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
- **BOOT:** Calibrate barometer, recieve waypoints from GCS, set home position to first GPS fix. Once boot is complete, the transmitter is in a safe state, and the vehicle has been armed, the system mode switches to flight.
- **FLIGHT:** Execute manual and auto modes

**Manual Modes**
- **DIRECT:** The pilot's control inputs (raw user inputs from RC transmitter) are passed directly to servos. No sensor feedback is used.
- **STABILIZED:** The pilot directly commands the roll and pitch angle. Thrust is directly set by the pilot.

**Auto Modes**
- **READY**: Detecting takeoff and waiting for flight. Throw the plane and then move the throttle up. After the throttle is full, the plane switches to TAKEOFF mode.
- **TAKEOFF** The throttle is set to the TAKEOFF_THR and the plane holds a pitch angle TAKEOFF_PTCH. The roll angle is set to 0 to prevent wingstrike. Once the altitude is above the takeoff altitude threshold TAKEOFF_ALT, the plane switches to MISSION mode. Only gyroscope dead reckoning for orientation.
- **MISSION:** Waypoint following. When passed final waypoint, the plane siwtches to LAND mode.
- **LAND:** Adjust altitude setpoint to follows a 10 degree glideslope to the landing point. Track heading is set to the runway heading. When the altitude is below LAND_FLARE_ALT, the plane switches to FLARE mode.
- **FLARE:** Throttle is cut, and the plane targets a sink rate of LAND_SINK with a maximum pitch angle of LAND_PITCH_DEG and a roll angle of 0 to prevent wing strike. When the gyroscopes observe no motion for 10 seconds, the plane switches to TOUCHDOWN mode.
- **SAFE:** Everything is turned off and disarmed for safe recovery of the aircraft. Maybe switch to safe system mode?

# Controller Theory

## Controller Tuning

First, a manual flight is flown collecting data of inputs and outputs (elevator vs pitch, aileron vs roll, pitch vs altitude, roll vs heading, etc). A Python program is used to fit the data with a transfer function and the P controllers are then designed for stability using frequency domain techniques.

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

| Payload Type   | Value |
| -------------- | ----- |
| Telemetry      | 0     |
| Command        | 1     |
| Waypoint       | 2     |
| Landing Target | 3     |

### Telemetry Payload

| Byte Index | Content          | Type     | Unit | Multiplier |
| ---------- | ---------------- | -------- | ---- | ---------- |
| 0          | Payload Type (0) | uint8_t  |
| 1-2        | Roll             | int16_t  | deg  | 1E-2       |
| 3-4        | Pitch            | int16_t  | deg  | 1E-2       |
| 5-6        | Heading          | uint16_t | deg  | 1E-2       |
| 7-8        | Altitude         | int16_t  | dm   |            |
| 9-10       | Airspeed         | int16_t  | dm/s |
| 11-14      | Latitude         | float    | deg  |
| 15-18      | Longitude        | float    | deg  |
| 19         | Mode ID          | uint8_t  |      |
| 20         | Target Waypoint Index | uint8_t |  |            |
| 21         | Cell Voltage     | uint8_t  | V    | 1E-2       |
| 22-37      | Empty            |          |      |      |

#### Mode IDs

| Mode | Name        |
| ---- | ----------- |
| 0    | BOOT        |
| 1    | -           |
| 2    | MANUAL      |
| 3    | STABILIZED  |
| 4    | READY       |
| 5    | TAKEOFF     |
| 6    | MISSION     |
| 7    | LAND        |
| 8    | FLARE       |

### Command Payload

| Byte Index | Content          | Type    |
| ---------- | ---------------- | ------- |
| 0          | Payload Type (1) | uint8_t |
| 1          | Command ID       | uint8_t |
| 2-37       | Empty            |         |

#### Command IDs

| Command ID           | Value |
| -------------------- |------ |
| Calibrate Gyroscopes | 0     |
| Calibrate Barometer  | 1     |

### Waypoint Payload

| Byte Index | Content          | Type    |
| ---------- | -----------------| ------- |
| 0          | Payload Type (2) | uint8_t |
| 1          | Waypoint Index   | uint8_t |
| 2-5        | Position North   | float   |
| 6-9        | Position East    | float   |
| 10-13      | Position Down    | float   |
| 14-37      | Empty            |         |

### Landing Target Payload

| Byte Index | Content          | Type    |
| ---------- | -----------------| ------- |
| 0          | Payload Type (3) | uint8_t |
| 1-4        | Latitude         | float   |
| 5-8        | Longitude        | float   |
| 9-12       | Heading          | float   |
| 13-37      | Empty            |         |

# Procedures

## Startup

Turn direct and mode switches to manual, cut throttle, and wait until GNSS fix and it will switch to from boot to manual mode.

## Takeoff

Cut throttle, mode switch down, manual switch down, to put in READY mode. Throw the plane and immediately move the throttle stick up. After the throttle stick is up, it will put the plane in TAKEOFF mode and climb automatically.

## Aborted Autoland

Toggle the manual switch and fly the plane in DIRECT mode. The plane will not switch back into auto mode after an aborted autoland. Only DIRECT and STABILIZE are available.

## Emergency

Flip the manual switch to put the plane into DIRECT mode.

# TODO
- Use Python to read data log in micro-SD card
- Commands to calibrate barometer, gyroscope, etc.
- Don't mix and match seconds and microseconds
