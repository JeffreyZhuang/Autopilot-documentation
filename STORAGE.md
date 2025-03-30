| Byte Index | Type | Content                           | Value   | Explanation |
|   -    |-   | --------------------------------- | ------- | - |
| 0        | `uint8_t` | Start byte                 | 0xFE | Used to indicate the beginning of a new packet |
| 1          | `uint8_t`| Payload Length                    | 1 - 255 | Indicates length of the payload section |
| 2           |`uint8_t` | Message ID | 1 - 255 | ID of message type in payload |
| For n-byte payload: 3 to (3+n)  | `uint8_t[max 255]`       | Payload   |       |  Message data. Depends on message type. |

Message types:
- Flight data
- Parameters (to remember what parameters you used during flight test)
- Waypoints (actually no just log the next waypoint lat/lon/alt as flight data setpoint)
