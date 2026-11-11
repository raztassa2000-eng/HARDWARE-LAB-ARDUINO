# Ball & Beam – PID Control System

A closed-loop controller that moves a ball to a target position on a tilting beam and holds it there.
An ultrasonic sensor measures the ball's distance, a PID loop computes the correction, and a servo tilts the beam.

Built by [Raz Tassa](https://www.linkedin.com/in/raz-tassa-3407722a6/) and
[Wasem Bkriya](https://www.linkedin.com/in/wasem-bk/) for the Hardware Exposure Lab, Tel Aviv University.

**Demo:** [ball and beam vid.mp4](ball%20and%20beam%20vid.mp4) · [LinkedIn post](https://www.linkedin.com/posts/raz-tassa-3407722a6_electricalengineering-controltheory-arduino-ugcPost-7401915491608354817-WUDr)

## How it works

```
ultrasonic sensor -> 10-sample moving average -> PID (every 40 ms) -> rate-limited servo -> beam tilt
        ^                                                                                      |
        +--------------------------------- ball position <------------------------------------+
```

- **Target:** 26.5 cm from the sensor.
- **Filtering:** a 10-sample moving average removes ultrasonic noise before the PID sees it.
- **PID:** Kp = 4.0, Ki = 0.20, Kd = 1.0, computed every 40 ms, output limited to ±50° around the servo centre.
- **Deadband:** within 1 cm of the target the beam returns to level, which stops the servo from chasing noise.
- **Rate limiting:** the servo moves at most 2° per loop near the target and 10° per loop when the error is above 10 cm.
- **Rescue mode:** below 5 cm the sensor is blind, so the beam tilts fully to roll the ball back into range.

## Hardware

| Part | Pin |
|---|---|
| Servo (beam tilt) | D9 |
| Ultrasonic sensor – TRIG | D10 |
| Ultrasonic sensor – ECHO | D11 |

## Files

| File | Contents |
|---|---|
| `PID_AdaptiveTunings.ino` | Controller firmware |
| `ball and beam vid.mp4` | Demo of the tuned system |
| Lab report (`.docx`, Hebrew) | System modelling, tuning process and preparation questions |

## Run it

1. Install the `PID` library by Brett Beauregard (`PID_v1`) in the Arduino IDE.
2. Wire the servo and sensor as above and upload `PID_AdaptiveTunings.ino`.
3. Open the Serial Plotter at 115200 baud to watch setpoint, measured position and servo angle.
