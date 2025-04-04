# State Machine

The state is stored in Autopilot/plane.h (Plane::system_mode, Plane::flight_mode, Plane::manual_mode). The state transitions can be found in Autopilot/Modules/Commander/commander.cpp. Each of the individual modules has an update function with a switch statement that determines what to execute depending on the plane's state. The update functions get called every loop iteration as seen in Autopilot/autopilot.cpp (Autopilot::main_task()).

**System Modes**
- **CONFIG:** Waits for parameters and waypoints to be loaded before switching system mode to BOOT. Nothing else gets executed.
- **BOOT:** Once AHRS and navigation has converged and the transmitter is in a safe state, the system mode switches to FLIGHT.
- **FLIGHT:** Executes manual and auto modes.

**Manual Modes**
- **DIRECT:** The pilot's control inputs (raw user inputs from RC transmitter) are passed directly to servos. No sensor feedback is used.
- **STABILIZED:** The pilot directly commands the roll and pitch angle. Thrust is directly set by the pilot.

**Auto Modes**
- **TAKEOFF** The throttle is set manually and the plane holds a pitch angle `Parameters::takeoff_ptch`. The roll angle is set to 0 to prevent wingstrike. Once the altitude is above the takeoff altitude threshold TAKEOFF_ALT, the plane switches to MISSION mode. Only gyroscope dead reckoning for orientation. The integrator in the control loops are disabled.
- **MISSION:** Waypoint following. When passed final waypoint, the plane siwtches to LAND mode.
- **LAND:** Adjust altitude setpoint to follows a 10 degree glideslope to the landing point. Track heading is set to the runway heading. When the altitude is below LAND_FLARE_ALT, the plane switches to FLARE mode.
- **FLARE:** Throttle is cut, and the plane targets a sink rate of LAND_SINK with a maximum pitch angle of LAND_PITCH_DEG and a roll angle of 0 to prevent wing strike. When the gyroscopes observe no motion for 10 seconds, the plane switches to TOUCHDOWN mode.
- **SAFE:** Everything is turned off and disarmed for safe recovery of the aircraft. Maybe switch to safe system mode?
