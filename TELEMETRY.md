# Packet Serialization

## Packet Format

| Byte Index | Content                           | Type    |
|   -       | --------------------------------- | ------- |
| 0          | Start byte (0x00)                 | uint8_t |
| 1           | Consistent Overhead Byte Stuffing | uint8_t |
| 2           | Payload Length                    | uint8_t |
| 3 to (3+n)           | Payload (1 - 255 bytes)           |         |
