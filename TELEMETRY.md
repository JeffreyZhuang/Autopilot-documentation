# Packet Serialization

## Packet Format

| Byte Index | Type | Content                           | Value   | Explanation |
|   -    |-   | --------------------------------- | ------- | - |
| 0        | `uint8_t` | Start byte (0x00)                 | 0x00 | Used to indicate the beginning of a new packet |
| 1          | `uint8_t`| Payload Length                    | 1 - 255 | Indicates length of the payload section |
| 2           |` uint8_t` | Message ID | 1 - 255 | ID of message type in payload |
| 3          |  `uint8_t`| COBS byte | 1 - 255 | Consistent Overhead Byte Stuffing (COBS) algorithm to prevent start byte from appearing in the middle of a packet |
| For n-byte payload: 4 to (4+n)  | `uint8_t[max 255]`       | Payload   |       |  Message data. Depends on message type. |
| (n+5) to (n+6) | `uint8_t[2]` | CRC-16 Checksum | | |

## Messages

### Telemetry (Message ID: 1)

| Content          | Type     | Unit |
| ---------------- | -------- | ---- |
| Roll             | int16_t  | $10^{-2}$ deg  |
| Pitch            | int16_t  | $10^{-2}$ deg  |
| Heading          | uint16_t | $10^{-2}$ deg  |
| Altitude         | int16_t  | dm   |          
| Airspeed         | uint16_t  | dm/s |
| GNSS Latitude         | int32_t    | $10^{-7}$ deg  |       
| GNSS Longitude        | int32_t    | $10^{-7}$ deg  |
| Position Estimate North | float | m |
| Position Estimate East |  float | m |
| Mode ID          | uint8_t  |      |
| Target Waypoint Index | uint8_t |  |         
| Cell Voltage     | uint16_t  | mV    |    
| Battery Current          | uint16_t  | mA    |   
| Capacity Consumed | uint16_t | mAh   | 
| Autopilot Current | uint16_t | mA |
| GPS Sats | uint8_t | |
| GPS Fix | bool | | 
| Aileron | uint8_t | %|
| Elevator | uint8_t | %|
| Throttle | uint8_t | %|

### Parameters (Message ID: 2)

### Waypoint (Message ID: 3)

| Content          | Type     | Unit |
| ---------------- | -------- | ---- |
| Waypoint Index   | uint8_t  |      |
| Total Number of Waypoints   | uint8_t  |      |
| Latitude   | int32_t  |   $10^{-7}$ deg   |
| Longitude | int32_t | $10^{-7}$ deg|
| Altitude | int16_t | dm | 
