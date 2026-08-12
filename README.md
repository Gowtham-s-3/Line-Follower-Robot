# Line Follower Robot

An autonomous line-following robot built using an **Arduino Nano**, **TB6612FNG motor driver**, **7-channel IR sensor array**, and **N20 geared motors**.

The robot detects a line using infrared sensors, calculates the position of the line, and continuously adjusts the left and right motor speeds using a **PID-based control algorithm**.

---

## 1. Project Overview

The main objective of this project is to build a compact autonomous robot capable of following a predefined line without manual control.

The control flow is:

```text
7-Channel IR Sensor Array
          ↓
      Arduino Nano
          ↓
   Sensor Calibration
          ↓
   Line Position Detection
          ↓
    Error Calculation
          ↓
      PID Control
          ↓
 Left / Right Motor Speed
          ↓
     TB6612FNG Driver
          ↓
       N20 Motors
```

---

## 2. Hardware Used

| Component                 | Quantity |
| ------------------------- | -------: |
| Arduino Nano              |        1 |
| TB6612FNG Motor Driver    |        1 |
| 7-Channel IR Sensor Array |        1 |
| N20 Geared DC Motor       |        2 |
| Battery                   |        1 |
| Caster Wheel              |        1 |
| Push Button               |        2 |
| LED                       |        1 |
| Robot Chassis             |        1 |

---

# 3. POWER CONNECTIONS

## ⚠️ IMPORTANT VOLTAGE WARNING

**Check the voltage and polarity before connecting the battery. Incorrect voltage or reversed polarity can permanently damage the Arduino, sensor array, motor driver, or other components.**

### Logic Supply — 5V

The following components use **5V logic/power**:

* Arduino Nano 5V rail
* TB6612FNG VCC
* IR sensor array VCC
* TB6612FNG STBY/enable signal

Use:

```text
5V → VCC
GND → GND
```

### Motor Supply — 7V to 7.4V

The motor supply is connected to the TB6612FNG **VM** pin.

Recommended battery voltage for this project:

```text
Battery + → TB6612FNG VM
Battery - → GND
```

A **7–7.4V nominal battery** can be used for the motor supply, provided the motor and driver ratings are appropriate.

### DO NOT DO THIS

```text
❌ 7.4V → Arduino 5V pin
❌ 7.4V → IR sensor VCC
❌ 7.4V → TB6612FNG VCC
```

The **5V rail must remain 5V**.

If a regulated 5V supply is used:

```text
5V regulated output → Arduino 5V
5V regulated output → Sensor VCC
5V regulated output → TB6612FNG VCC
GND → Common GND
```

If a 7–7.4V battery is used for the motors:

```text
7–7.4V battery + → TB6612FNG VM
Battery - → Common GND
```

**All grounds must be common.**

```text
Arduino GND
     │
     ├── TB6612FNG GND
     ├── IR Sensor GND
     └── Battery -
```

> **Never connect the 7–7.4V motor supply directly to a 5V pin.**

---

# 4. TB6612FNG → Arduino Nano CONNECTIONS

The following pin mapping is based on the **actual pin definitions in `LF2_Line_Follower.ino`**.

| TB6612FNG | Arduino Nano | Function              |
| --------- | ------------ | --------------------- |
| AIN1      | D4           | Motor A direction     |
| AIN2      | D3           | Motor A direction     |
| PWMA      | D9           | Motor A PWM           |
| BIN1      | D6           | Motor B direction     |
| BIN2      | D7           | Motor B direction     |
| PWMB      | D10          | Motor B PWM           |
| STBY      | D5           | Driver standby/enable |
| VCC       | 5V           | Logic supply          |
| GND       | GND          | Common ground         |
| VM        | 7–7.4V       | Motor supply          |

### Important

The source code contains:

```cpp
#define AIN1 4
#define BIN1 6
#define AIN2 3
#define BIN2 7
#define PWMA 9
#define PWMB 10
```

The code also enables the motor driver using:

```cpp
pinMode(5, OUTPUT);
digitalWrite(5, HIGH);
```

Therefore, **D5 is being used as the TB6612FNG standby/enable signal in the current code.**

---

# 5. N20 MOTOR CONNECTIONS

Connect the motors to the TB6612FNG motor outputs.

```text
TB6612FNG A01/A02 → Left N20 Motor
TB6612FNG B01/B02 → Right N20 Motor
```

If one motor rotates in the opposite direction from what the software expects, the motor wires can be reversed for that motor.

Do **not** change the battery polarity to correct motor direction.

---

# 6. IR SENSOR ARRAY CONNECTIONS

The current code supports **5 or 7 sensors**.

The default configuration is:

```cpp
unsigned int numSensors = 5;
```

The sensor inputs are:

| Sensor   | Arduino Nano |
| -------- | ------------ |
| Sensor 1 | A0           |
| Sensor 2 | A1           |
| Sensor 3 | A2           |
| Sensor 4 | A3           |
| Sensor 5 | A4           |
| Sensor 6 | A5           |
| Sensor 7 | A6           |

Power:

```text
IR VCC → 5V
IR GND → GND
```

The uploaded wiring document also specifies the seven sensors as A0 through A6.

---

# 7. PUSH BUTTONS

Two push buttons are used.

| Function           | Arduino Pin |
| ------------------ | ----------- |
| Calibration Button | D11         |
| Start Button       | D12         |

Both buttons connect to:

```text
Button → Arduino pin
Other side → GND
```

The code uses:

```cpp
pinMode(11, INPUT_PULLUP);
pinMode(12, INPUT_PULLUP);
```

Therefore, the buttons are **active LOW**.

The uploaded wiring document also identifies D11 as the calibration button and D12 as the start button.

---

# 8. LED INDICATOR

The built-in/status LED is connected to:

```text
Arduino Nano D13 → LED
```

The code configures:

```cpp
pinMode(13, OUTPUT);
```

The LED is turned ON while the robot is detecting/following the line and OFF when the line is lost.

The wiring document also specifies D13 for the LED indicator.

---

# 9. COMPLETE PIN TABLE

| Arduino Nano | Connected Component | Purpose             |
| ------------ | ------------------- | ------------------- |
| A0           | IR Sensor 1         | Line detection      |
| A1           | IR Sensor 2         | Line detection      |
| A2           | IR Sensor 3         | Line detection      |
| A3           | IR Sensor 4         | Line detection      |
| A4           | IR Sensor 5         | Line detection      |
| A5           | IR Sensor 6         | Line detection      |
| A6           | IR Sensor 7         | Line detection      |
| D3           | TB6612 AIN2         | Motor A direction   |
| D4           | TB6612 AIN1         | Motor A direction   |
| D5           | TB6612 STBY         | Motor driver enable |
| D6           | TB6612 BIN1         | Motor B direction   |
| D7           | TB6612 BIN2         | Motor B direction   |
| D9           | TB6612 PWMA         | Motor A PWM         |
| D10          | TB6612 PWMB         | Motor B PWM         |
| D11          | Push Button         | Calibration         |
| D12          | Push Button         | Start               |
| D13          | LED                 | Status              |

---

# 10. SOFTWARE

### Development Environment

* Arduino IDE
* Arduino C/C++
* Embedded control logic

### Main Software Functions

The program performs:

1. Sensor calibration
2. Analog sensor reading
3. Line detection
4. Error calculation
5. PID calculation
6. Left/right motor speed calculation
7. PWM motor control
8. Line-loss recovery

---

# 11. CALIBRATION PROCESS

Before following the line, the robot performs sensor calibration.

Press the **Calibration Button on D11**.

The program then:

```text
Start Calibration
       ↓
Read minimum sensor values
       ↓
Read maximum sensor values
       ↓
Rotate the robot
       ↓
Collect sensor readings
       ↓
Calculate threshold values
       ↓
Stop motors
       ↓
Calibration complete
```

The code performs 10,000 calibration iterations while rotating the robot:

```cpp
motor1run(50);
motor2run(-50);
```

The minimum and maximum values are stored for each sensor.

The threshold is calculated as:

```text
Threshold = (Minimum + Maximum) / 2
```

---

# 12. STARTING THE ROBOT

After calibration:

1. Release/complete the calibration operation.
2. Press the **Start Button on D12**.
3. The robot begins line following.

The start button uses `INPUT_PULLUP`, so pressing the button connects the input to GND.

---

# 13. LINE DETECTION

The sensor values are normalized to a range of:

```text
0 → 1000
```

The program uses:

```cpp
sensorArray[i] = sensorValue[i] > 500;
```

to determine whether a sensor is active.

The current configuration is:

```cpp
bool isBlackLine = 1;
```

Therefore, the current program is configured for a **black line**.

For a white line, this setting can be changed to:

```cpp
bool isBlackLine = 0;
```

---

# 14. PID CONTROL

The robot calculates an error based on the position of the detected line.

The sensor weights are:

```text
+4   +2   +1    0   -1   -2   -4
```

The center sensor has a weight of zero.

The controller uses:

```text
P = Current Error
I = Accumulated Error
D = Change in Error
```

The PID output is calculated using:

```text
PID = Kp × P + Ki × I + Kd × D
```

Current values in the source code:

```cpp
Kp = 0.08;
Kd = 0.15;
Ki = 0;
```

The resulting PID value is used to adjust the left and right motor speeds.

---

# 15. MOTOR SPEED CONTROL

The default maximum line-following speed is:

```cpp
int lfSpeed = 150;
```

The robot gradually increases its current speed:

```cpp
if (currentSpeed < lfSpeed)
    currentSpeed++;
```

The motor speeds are calculated as:

```text
Left Motor  = Current Speed - PID
Right Motor = Current Speed + PID
```

The PWM values are limited to:

```text
0 → 255
```

This prevents the PWM output from exceeding the Arduino PWM range used by the program.

---

# 16. LINE LOST CONDITION

If the sensors no longer detect the line, the program attempts to recover the line.

Depending on the direction of the previous error, the robot turns in the corresponding direction until the line is detected again.

---

# 17. MOTOR CONTROL LOGIC

The program supports:

```text
Positive speed → Forward
Negative speed → Reverse
Zero speed     → Stop
```

The TB6612FNG direction pins control the motor direction, while PWMA/PWMB control motor speed through PWM.

---

# 18. IMPORTANT WIRING CHECKLIST

Before switching ON the battery, check **every item**.

* [ ] Battery polarity is correct.
* [ ] Arduino GND and motor-driver GND are connected.
* [ ] Sensor GND is connected to common GND.
* [ ] TB6612FNG VCC is connected to **5V**.
* [ ] IR sensor VCC is connected to **5V**.
* [ ] Motor supply VM is connected to the intended **7–7.4V motor supply**.
* [ ] **7–7.4V is NOT connected to a 5V pin.**
* [ ] TB6612FNG STBY is connected to **D5** according to the current source code.
* [ ] AIN1 is connected to D4.
* [ ] AIN2 is connected to D3.
* [ ] PWMA is connected to D9.
* [ ] BIN1 is connected to D6.
* [ ] BIN2 is connected to D7.
* [ ] PWMB is connected to D10.
* [ ] IR sensors are connected to A0–A6.
* [ ] Calibration button is connected to D11 and GND.
* [ ] Start button is connected to D12 and GND.
* [ ] LED is connected to D13.
* [ ] Motors are connected to A01/A02 and B01/B02.
* [ ] No motor-supply voltage is accidentally connected to the sensor VCC or Arduino 5V rail.

---

# 19. ⚠️ IMPORTANT: SOURCE CODE AND WIRING DOCUMENT

The current source code and the original wiring PDF contain **different motor-driver pin assignments**.

### Current source code

```text
AIN1 → D4
AIN2 → D3
PWMA → D9

BIN1 → D6
BIN2 → D7
PWMB → D10

STBY → D5
```

### Original wiring PDF

The PDF shows a different assignment for the motor-driver control pins.

**For the robot to operate correctly with the current `LF2_Line_Follower.ino`, follow the pin table in this README and the actual source code.**

Do not connect the motor driver according to an outdated pin diagram and then run the current code.

---

# 20. Project Files

```text
Line-Follower-Robot/
│
├── docs/
│   └── wiring_connections.pdf
│
├── images/
│   ├── robot photos
│   └── demonstration media
│
└── src/
    └── LF2_Line_Follower/
        ├── LF2_Line_Follower.ino
        ├── ReadMe.adoc
        └── sketch.json
```

---

# 21. How to Upload the Code

1. Install/open Arduino IDE.
2. Open `LF2_Line_Follower.ino`.
3. Select the correct Arduino Nano board.
4. Select the correct COM port.
5. Verify the wiring.
6. Connect the Arduino to the computer through USB.
7. Upload the program.
8. Disconnect USB if the robot is being powered from its battery.
9. Perform the wiring and voltage checks.
10. Power the robot.
11. Press the calibration button.
12. After calibration, press the start button.

---

# 22. Safety

The most important rule is:

> **Always verify voltage, polarity, and common ground before connecting the battery.**

Use **5V for the logic/sensor supply** and approximately **7–7.4V for the motor supply** when that voltage is appropriate for the selected motors and battery.

**Never connect 7–7.4V directly to a 5V input.**

If there is any uncertainty about the battery or power wiring, disconnect the battery and verify the voltage with a multimeter before powering the circuit.

---

# 23. Project Result

The completed system provides autonomous line following using:

* Arduino Nano
* 7-channel IR sensing
* Sensor calibration
* PID-based line-following control
* TB6612FNG dual motor driver
* N20 geared motors
* PWM speed control
* Line-loss recovery
* Calibration and start buttons
* LED status indication

---

## Author

**Gowtham S**

Mechatronics Engineering

## License

This project is provided for educational and project demonstration purposes.
