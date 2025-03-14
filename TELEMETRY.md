# Packet Serialization

## Packet Format

| Byte Index | Type | Content                           | Value   | Explanation |
|   -    |-   | --------------------------------- | ------- | - |
| 0        |  uint8_t | Start byte (0x00)                 | 0x00 | Used to indicate the beginning of a new packet |
| 1          | uint8_t| Payload Length                    | 1 - 255 | Indicates length of the payload section |
| 2           | uint8_t | Message ID | 1 - 255 | ID of message type in payload |
| 3          |  uint8_t| COBS byte | 1 - 255 | Consistent Overhead Byte Stuffing (COBS) algorithm to prevent start byte from appearing in the middle of a packet |
| For n-byte payload: 4 to (4+n)  | uint8_t[max 255]       | Payload   |       |  Message data. Depends on message type. |

## Messages

### Telemetry (Message ID: 1)

| Content          | Type     | Unit | Multiplier |
| ---------------- | -------- | ---- | ---------- |
| Payload Type (0) | uint8_t  |
| Roll             | int16_t  | deg  | 1E-1       |
| Pitch            | int16_t  | deg  | 1E-1       |
| Heading          | uint16_t | deg  | 1E-1       |
| Altitude         | int16_t  | dm   |            |
| Airspeed         | int16_t  | dm/s | |
| Latitude         | float    | deg  |         |
| Longitude        | float    | deg  |
| Mode ID          | uint8_t  |      |
| Target Waypoint Index | uint8_t |  |            |
| Cell Voltage     | uint8_t  | V    | 5E-1       |
| Current          | uint8_t  | A    | 5E1        |
| Capacity Consumed | uint8_t | Ah   | |
| GPS Sats | uint8_t |  | |
| GPS Fix | uint8_t | | | 
