# Packet Serialization

## Packet Format

| Byte Index | Type | Content                           | Value   |
|   -    |-   | --------------------------------- | ------- |
| 0        |  uint8_t | Start byte (0x00)                 | uint8_t |
| 1         |  uint8_t| Consistent Overhead Byte Stuffing | uint8_t |
| 2          | uint8_t| Payload Length                    | uint8_t |
| 3 to (3+n)  | uint8_t[max 255]       | Payload (1 - 255 bytes)   | uint8_t[max 255]         |
