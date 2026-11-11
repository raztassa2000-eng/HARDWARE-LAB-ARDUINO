# Face-Tracking Pan/Tilt System

A camera mount that keeps a person's face centred in the frame.
A Python/OpenCV script detects the face and sends its position over UART to an Arduino, which steps two servos (pan and tilt) toward it.

Built by [Raz Tassa](https://www.linkedin.com/in/raz-tassa-3407722a6/) and
[Wasem Bkriya](https://www.linkedin.com/in/wasem-bk/) for the Hardware Exposure Lab, Tel Aviv University.
The host script is adapted from a face tracker by Shubham Santosh, with an added dead zone for stability.

**Demo:** see the `.mp4` in this folder.

## How it works

```
webcam -> OpenCV Haar-cascade face detection -> face centre (x, y)
       -> outside dead zone?  -> "X<x>Y<y>" over UART, 9600 baud
       -> Arduino parses the message -> pan / tilt servos step 2° toward the face
```

- **Detection:** OpenCV Haar cascade on a mirrored 640×480 greyscale frame.
- **Dead zone (host):** no command is sent while the face centre is within 80 px of the frame centre, so the servos stay still once the face is locked. The on-screen box turns green.
- **Protocol:** plain-text `X<int>Y<int>` messages, parsed on the Arduino with `Serial.parseInt()`.
- **Motion (Arduino):** each message moves a servo by 2° if the face is more than 30 px off-centre on that axis; angles are clamped to 0–180°.

## Hardware

| Part | Pin |
|---|---|
| Pan servo | D9 |
| Tilt servo | D10 |
| USB serial to host PC | 9600 baud |

## Files

| File | Contents |
|---|---|
| `face_tracking.py` | Host script: detection, dead zone, serial output |
| `face_rec_dec9a.ino` | Arduino firmware: message parsing and servo control |
| Lab report (`.docx`, Hebrew) | Design notes and results |

## Run it

```bash
pip install opencv-python pyserial
python face_tracking.py
```

Set the serial port in `face_tracking.py` (default `com4`) to match your Arduino. Press `q` to quit.
