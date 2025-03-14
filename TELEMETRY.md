# Packet Serialization

## Packet Format

| Byte Index | Type | Content                           | Value   | Explanation |
|   -    |-   | --------------------------------- | ------- | - |
| 0        |  uint8_t | Start byte (0x00)                 | 0x00 | Used to indicate the beginning of a new packet |
| 1         |  uint8_t| COBS byte |  | Consistent Overhead Byte Stuffing (COBS) algorithm to prevent start byte from appearing in the middle of a packet |
| 2          | uint8_t| Payload Length                    | 1-255 | Indicates length of the payload section |
| For n-byte payload: 3 to (3+n)  | uint8_t[max 255]       | Payload (1 - 255 bytes)   |       |  Message data. Depends on message type. |
