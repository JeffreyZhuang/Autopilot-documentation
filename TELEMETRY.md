# Packet Serialization

## Packet Format

| Byte Index | Type | Content                           | Value   |
|   -    |-   | --------------------------------- | ------- |
| 0        |  uint8_t | Start byte (0x00)                 | 0x00 |
| 1         |  uint8_t| Consistent Overhead Byte Stuffing |  |
| 2          | uint8_t| Payload Length                    | 1-255 |
| For n-byte payload: 3 to (3+n)  | uint8_t[max 255]       | Payload (1 - 255 bytes)   |          |
