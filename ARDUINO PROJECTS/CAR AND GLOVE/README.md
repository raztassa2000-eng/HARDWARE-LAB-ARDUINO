# Gesture-Controlled Robotic Vehicle

A glove with an IMU that drives a small car: tilt your hand forward, back, left or right and the car follows.
The glove reads an MPU6050 accelerometer and sends drive commands over an nRF24L01 radio link.

Built by [Raz Tassa](https://www.linkedin.com/in/raz-tassa-3407722a6/) and
[Wasem Bkriya](https://www.linkedin.com/in/wasem-bk/) for the Hardware Exposure Lab, Tel Aviv University.

> This folder currently holds the glove (transmitter) firmware only. The car (receiver) firmware has not been uploaded yet.

## How it works

```
MPU6050 (I2C) -> raw X/Y acceleration -> tilt thresholds -> command 0-4 -> nRF24L01 -> car
```

- **Gesture mapping:** raw acceleration beyond ±8000 on X or Y selects a command; anything inside that band is a dead zone and sends "stop".

  | Tilt | Command |
  |---|---|
  | none (dead zone) | 0 – stop |
  | forward | 1 |
  | back | 2 |
  | left | 3 |
  | right | 4 |

- **Radio link:** packets are sent every 100 ms with NRFLite, which uses the nRF24's hardware CRC and acknowledgements. Unacknowledged packets are counted in `FailedTxCount`.
- **Fault handling:** on start-up the firmware scans the I2C bus for the MPU6050 at `0x68` and reports wiring errors; at run time it detects a disconnected sensor and stops sending.

## Wiring (Arduino Uno)

| Module | Connections |
|---|---|
| MPU6050 | VCC → 5V, GND → GND, SDA → A4, SCL → A5 |
| nRF24L01 | VCC → **3.3V only**, CE → D7, CSN → D8, SCK → D13, MOSI → D11, MISO → D12 |

## Files

| File | Contents |
|---|---|
| `TSM.ino` | Glove transmitter firmware |

## Run it

1. Install the `TinyMPU6050` and `NRFLite` libraries in the Arduino IDE.
2. Wire the modules as above and upload `TSM.ino`.
3. Keep the glove still during the start-up calibration, then open the Serial Monitor at 9600 baud to see the detected gesture.
