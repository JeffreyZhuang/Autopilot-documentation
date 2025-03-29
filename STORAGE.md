Packet format: 

- Start byte (0x00)
- uint8_t msg_id
- uint16_t msg_len
- COBS byte
- Packet

Message types:
- Flight data
- Parameters (to remember what parameters you used during flight test)
- Waypoints (actually no just log the next waypoint lat/lon/alt as flight data setpoint)
