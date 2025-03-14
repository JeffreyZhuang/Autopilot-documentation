# Packet Serialization

## Packet Format

| Byte Index | Type | Content                           | Value   | Explanation |
|   -    |-   | --------------------------------- | ------- | - |
| 0        |  uint8_t | Start byte (0x00)                 | 0x00 | Used to indicate the beginning of a new packet |
| 1          | uint8_t| Payload Length                    | 1 - 255 | Indicates length of the payload section |
| 2           | uint8_t | Message ID | 1 - 255 | |
| 3          |  uint8_t| COBS byte | 1 - 255 | Consistent Overhead Byte Stuffing (COBS) algorithm to prevent start byte from appearing in the middle of a packet |
| For n-byte payload: 4 to (4+n)  | uint8_t[max 255]       | Payload   |       |  Message data. Depends on message type. |
