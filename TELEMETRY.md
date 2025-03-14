# Packet Serialization

## Packet Format

| Content                            | Type    |
| --------------------------------- | ------- |
| Start byte (0x00)                 | uint8_t |
| Consistent Overhead Byte Stuffing | uint8_t |
| Payload Length                    | uint8_t |
| Payload (1 - 255 bytes)           |         |
