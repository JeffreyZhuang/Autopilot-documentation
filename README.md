# Autopilot Documentation

<p float="left">
  <img src="images/enclosure.webp" width="20%"/>
  <img src="images/mainboard.webp" width="20%"/>
</p>

# Scope

The goal is to write simple code for an RC plane autopilot that is easy to understand, easy to trace, and follows best practices for safety-critical embedded firmware.
- For small hand-launched and belly landing fixed-wing UAVs
- Automatic takeoff, waypoint following, and landing
- Maximum operation time of 24 hrs
- Maximum distance from home of 50 km
- Maximum altitude of 3 km
- Maximum number of waypoints of 255
- The GCS is mandatory for operation like most military UAVs
- A takeoff point, landing point, and minimum of 1 waypoint required for operation
- Extra servo channels for payloads
- The parameters are loaded through radio. This is more convenient and also safer compared to modifying the firmware because you might make mistake in firmware.
- 8 PWM output channels
- PWM Channel 1, 2, and 3 reserved for aileron, elevator, and throttle. Re-arrange wires to swap, not in software.
- RC Channel 1, 2, 3, 4, 5, and 6 is aileron, elevator, rudder, throttle, manual switch, and mode switch respectively. Change settings in transmitter, not parameters. Only those 6 input channels are supported.
- You can calibrate mag and accel by sending raw data through telemetry... GCS requests or there is a calibration page before the parameters page
- Only 50Hz PWM servos
- No flaps or landing gear, only aileron elevator rudder throttle
- Both Optical flow and baro-only operation

# Sensors
- Gyroscope
- Accelerometer
- Magnetometer
- Barometer
- GNSS
- Optical Flow

# Coordinate System

<img src="images/coordinate-system.png" width="45%"/>

The Kalman filter calculates the position in meters in reference to the position of the first GPS fix.

# Software Architecture

# Diagram

# AHRS

Calculates roll, pitch, heading

# Commander

Detects state transitions and changes states

# Control

Calculates control surface commands

# Guidance

Calculates heading and altitude setpoints from waypoints and switches between waypoints

# Mixer

Converts control surface commands into PWM values (decides between rudder or ailerons) and sends them to servos

# Navigation

Kalman filter to calculate position and velocity of the plane

# RC

Reads data from RC transmitter and converts stick and switch PWM values into usable variables for plane

# Storage

Stores data into buffer and writes buffer to storage when full

# TECS

Calculates energy, energy setpoint, energy balance, and energy balance setpoints for the altitude and speed controllers to manage simultaneous control of airspeed and altitude

# Telemetry

Parses messages from GCS and transmits telemetry

### Telemetry

## Tasks

There are two tasks that need to run simultaneously, a main task and a background task. The main task is where the time critical autopilot code is run, and the background task is for writting large buffers of multiple chunks to micro-SD card using DMA. For STM32 HAL, the main task is executed from a hardware timer interrupt at 100Hz and the background task is executed by a while loop in main. 

## Update Rates

The main loop and PID controllers run at 100Hz.

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
| 1-2        | Roll             | int16_t  | deg  | 1E-1       |
| 3-4        | Pitch            | int16_t  | deg  | 1E-1       |
| 5-6        | Heading          | uint16_t | deg  | 1E-1       |
| 7-8        | Altitude         | int16_t  | dm   |            |
| 9-10       | Airspeed         | int16_t  | dm/s |
| 11-14      | Latitude         | float    | deg  |
| 15-18      | Longitude        | float    | deg  |
| 19         | Mode ID          | uint8_t  |      |
| 20         | Target Waypoint Index | uint8_t |  |            |
| 21         | Cell Voltage     | uint8_t  | V    | 5E-1       |
| 22         | Current          | uint8_t  | A    | 5E1        |
| 23         | Capacity Consumed | uint8_t | Ah   | |
| 24         | GPS Sats | uint8_t |  | |
| 25         | GPS Fix | uint8_t | | | 
| 26-37      | Empty            |          |      |      |

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

# Storage

| Byte Index | Content                            | Type      | Unit  |
| ---------- | ---------------------------------- | --------- | ----- |
| 0          | Start byte (0x00)                  | uint8_t   |       |
| 1          | Consistent Overhead Byte Shuffling | uint8_t   |       |
| 2-13       | Gyroscope     X-Z                  | float[3]  | deg/s |
| 14-25      | Accelerometer X-Z                  | float[3]  | g     |
| 14-25      | Magnetometer  X-Z                  | float[3]  | uT    |
| 26-41      | Raw GNSS Lat/Lon                   | double[2] | deg   |
| 42-53      | Position Estimate NED              | float[3]  | m     |
| 54-65      | Velocity Estimate NED              | float[3]  | m/s   |
| 66-77      | Orientation RPY                    | float[3]  | deg   |
| 66-77      | System Mode                        | uint8_t   |       |
| 66-77      | Auto Mode                          | uint8_t   |       |
| 66-77      | Manual Mode                        | uint8_t   |       |
| 66-77      | Battery Voltage                    | uint8_t   |       |
| 66-77      | Battery Current                    | uint8_t   |       |
| 66-77      | Autopilot Current                  | uint8_t   |       |
| 66-77      | Elevator                           | uint8_t   |       |
| 66-77      | Aileron                            | uint8_t   |       |
| 66-77      | Throttle                           | uint8_t   |       |
| 66-77      | Orientation Setpoint RPY           | float[3]  | deg   |
| 66-77      | Altitude setopint                  | float     | m     |
| 66-77      | Waypoint Lat/Lon                   | double[2] | deg   |
| 66-77      | Waypoint Altitude                  | float     | m   |

# Loading parameters

The struct in parameters.h is sent through radio
 
# Calibration

Use recorded data for compass, accelerometer, and gyroscope calibration.
Use recorded data to find RC transmitter endpoints to update parameters JSON.

# ESC Calibration

In DIRECT mode, move sticks to calibrate ESC manually.

# Procedures

## Prep

Make sure micro-SD card inserted

## Startup

Turn direct and mode switches to manual, cut throttle, and wait until GNSS fix and it will switch to from boot to manual mode.
Put it into stabalize mode to make sure control directions are correct.

## Takeoff

Cut throttle, mode switch down, manual switch down, to put in READY mode. Throw the plane and immediately move the throttle stick up. After the throttle stick is up, it will put the plane in TAKEOFF mode and climb automatically.

## Aborted Autoland

Toggle the manual switch and fly the plane in DIRECT mode. The plane will not switch back into auto mode after an aborted autoland. Only DIRECT and STABILIZE are available.

## Emergency

Flip the manual switch to put the plane into DIRECT mode.

## After Crash

Turn motor off. Look at current draw to make sure its off.

# Learning Resources

# TODO
- Use Python to read data log in micro-SD card
- Commands to calibrate barometer, gyroscope, etc.
- Don't mix and match seconds and microseconds
