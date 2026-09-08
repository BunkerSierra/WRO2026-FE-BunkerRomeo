# Bunker Romeo Team – WRO Future Engineers 2026

![WRO](https://img.shields.io/badge/WRO-Future%20Engineers%202026-0057B7?style=for-the-badge) ![Country](https://img.shields.io/badge/Baja%20California-Mexico-006341?style=for-the-badge) ![Status](https://img.shields.io/badge/Status-In%20development-yellow?style=for-the-badge) ![Controller](https://img.shields.io/badge/Controller-Arduino%20Mega%202560-00979D?style=for-the-badge)

<a id="indicleto"></a>
 
## Table of Contents
 
1.  [About the Team](#about-the-team)
2.  [Project Overview](#resumen-proyecto)
3.  [1. Mobility and Mechanical Design](#movilidad-mecanico)
   - [Mechanical Design Parts (CAD)](#piezas-cad)
   - [Vehicle Photos (Current State)](#fotos-del-vehiculo)
4.  [2. Power and Sensor Architecture](#arquitectura-potencia)
5.  [3. Software Architecture and Obstacle Strategy](#arquitectura-software)
   - [3.1 Open Challenge](#open-challenge-sw)
   - [3.2 Obstacle Challenge](#obstacle-challenge-sw)
     - [1. Challenge framework and design goals](#marco-del-reto)
     - [2. General architecture: state machine](#maquina-de-estados)
     - [3. Sensor suite and role assignment](#sensores-obstaculos)
     - [4. Straight-heading control](#control-rumbo-recto)
     - [5. Pillar tracking](#seguimiento-pilares)
     - [6. Dodge decision](#decision-sorteo)
     - [7. Corner detection](#deteccion-esquinas)
     - [8. Corner turn](#giro-esquina)
     - [9. Self-adjustment from the servo profile](#auto-ajuste-servo)
     - [10. Defensive engineering](#ingenieria-defensiva)
     - [11. Odometry and counter separation](#odometria)
     - [12. Methodology and reversed decisions](#metodologia-decisiones-revertidas)
6.  [4. Systems Thinking and Engineering Decisions](#bitacora-decisiones)
7.  [5. Reproducibility and Repository Structure](#reproducibilidad)
8.  [Competition Videos](#competition-videos)
   - [Open Challenge](#open-challenge)
   - [Obstacle Challenge](#obstacle-challenge)
9.  [BOM (Bill of Materials)](#bom)
---
 
## About the Team

We are **Bunker Romeo Team**, from Bunker Robotics, in Baja California, Mexico. This is our second year competing in the Future Engineers category, and we started preparing for this season in January 2025. Between the two of us we bring experience from the Robomission Junior, Robomission Senior and Future Engineers categories.

<div align="center">
<img src="/t-photos/EQUIPOROMEO.jpeg" width="480" alt="Bunker Romeo Team">
</div>

### Jacobo Arteaga Castañeda

**Age:** 21
**Role:** Robot operation and performance

I have competed in WRO since I was 13, going through the Robomission Junior and Robomission Senior categories and, since the 2025 season, Future Engineers. I have had the opportunity to represent Mexico at the International Final twice: in 2021 and 2025. Within the team I am responsible for operating the robot on the track — the setup before each round, handling the vehicle during test runs, and reading the robot's real behavior, which is where most of the adjustments documented in this repository's decision log come from.

### Ian Fernando Rivera Armenta

**Age:** 18
**Role:** Documentation and repository

I have been in the competition for one year. I prepared for Robomission Senior in the 2024 season and debuted in Future Engineers, where in my first regional we were recognized as the best team. Within the team I am responsible for the repository and the engineering documentation: keeping a record of every design decision, its rationale and the evidence that backs it up, as well as organizing the code and technical files published here.

[⬆ Back to top](#indicleto)
 
---
 
<a id="resumen-proyecto"></a>

## Project Overview

Autonomous vehicle developed for the **WRO Future Engineers 2026** category, built on an Arduino Mega 2560 with single-motor rear-wheel drive and servo-actuated steering. The robot solves the two challenges with different navigation strategies: PID wall following in the Open Challenge, and a state machine with color vision in the Obstacle Challenge.

| | |
|---|---|
| **Dimensions** | 18.8 × 14.6 × 20.3 cm (length × width × height) — regulation limit: 30 × 20 × 30 cm |
| **Weight** | 817 g — regulation limit: 1.5 kg |
| **Drivetrain** | GA37-520 motor (300 RPM) with 1:1 transmission to the wheels |
| **Perception** | 2 × VL53L0X (lateral ToF), HC-SR04P (front), MPU9250 (heading), HuskyLens (color), encoder (odometry) |
| **Best Open Challenge time** | **75 s** (3 laps + stop in the starting section) |
| **Obstacle Challenge run** | **2 min 18 s** complete, autonomous, without displacing any traffic sign |

**Measured results that back up the design decisions:**

- Open Challenge time reduced from **95 s → 75 s** (≈21 %) by migrating navigation from gyroscope heading to wall following with laser sensors, which allowed us to raise the robot's speed without losing trajectory accuracy (Decision 9).
- Correction of a mechanical source of drift that pushed the straight-line trajectory off course, eliminating the robot's constant self-corrections on long stretches (Decision 13).
- Chassis footprint reduced by 2 cm in length and 2 cm in width compared with the previous season (Decision 3).
- Total height reduced from 23.9 cm to **20.3 cm** with the wheel change (Decision 3).

[⬆ Back to top](#indicleto)
 
---
 
<a id="movilidad-mecanico"></a>

## 1. Mobility and Mechanical Design

> [!NOTE]
> The reasoning behind each mechanical decision —what alternatives were considered and why each solution was chosen— is documented in detail in the [Engineering Decision Log](#bitacora-decisiones).

| Aspect | Current specification |
|---|---|
| Wheels | 57 × 14 mm (Lego Spike Prime) |
| Total height | 20.3 cm |
| Total length | 18.8 cm |
| Total width | 14.6 cm |
| Total weight | 817 g |
| Main controller | Arduino Mega 2560 |

> [!NOTE]
> The chassis was redesigned from an earlier version that used 62.4 × 20 mm wheels (height 23.9 cm, width 15 cm). Full reasoning in the Decision Log, **Decision 3**.

> [!NOTE]
> We corrected an undersized axle hole in the printed motor mount, which caused excessive drift (~45°) when driving in a straight line. The drift was considerably reduced after the fix, although it still persists to a lesser degree. Full reasoning in the Decision Log, **Decision 13**.

### Wheel speed and torque analysis

The transmission between the motor and the wheels uses **1:1** gears (the same number of teeth on both, so they turn at the same angular speed) — that is, the wheel RPM is the same as the motor RPM, with no reduction or multiplication.

**Theoretical linear speed.** With the **GA37-520** motor at **300 RPM** (datasheet) and 57 mm diameter wheels:

```
v = π × D_wheel × RPM / 60
v = π × 0.057 m × 300 / 60
v ≈ 0.895 m/s  (≈ 89.5 cm/s ≈ 3.22 km/h)
```

This is the maximum theoretical linear speed with no load; on the track, the real speed is lower because of friction, the robot's weight and PWM control.

**Wheel torque.** The BOM reports a motor power range of **5.55–16.65 W**. At 300 RPM, the angular speed is ω = 2π × 300/60 ≈ 31.42 rad/s. With τ = P/ω:

| | Power | Torque (τ = P/ω) | Force at the ground (F = τ / r, r = 28.5 mm) |
|---|---|---|---|
| Minimum | 5.55 W | ≈ 0.177 N·m (≈ 1.80 kgf·cm) | ≈ 6.2 N (≈ 0.63 kgf) |
| Maximum | 16.65 W | ≈ 0.530 N·m (≈ 5.40 kgf·cm) | ≈ 18.6 N (≈ 1.90 kgf) |

Since the gear ratio is 1:1, the torque available at the wheel is the same as the motor's output torque — there is no mechanical gain or loss from a gear ratio, unlike a reduction gearbox.

**Relationship with the empirical result.** The theoretical speed above can only be exploited if the robot can run close to it without losing its heading, and that is where the mechanical analysis connects with the navigation decisions. With a 1:1 transmission there is no reduction to damp out trajectory errors: every degree of drift translates directly into extra distance travelled and time lost self-correcting. That is why, as long as navigation relied on gyroscope heading, the speed had to be kept low so the accumulated error would not grow — and the run took **95 seconds**. When we moved to wall following (Decision Log, **Decision 9**), the reference stopped accumulating error and we were able to raise the speed and bring the run down to **75 seconds**. The later mechanical drift correction (**Decision 13**) attacked the same problem from the hardware side, allowing that speed to be sustained in a straight line.

<a id="piezas-cad"></a>

### Mechanical Design Parts (CAD)

> [!NOTE]
> All the `.STL` files referenced here are available in the repository's [`/cad`](cad/) folder. The dimensions of each part (bounding box) were taken directly from the 3D file; they are not estimates.

#### Laser sensor mount (VL53L0X)

The 3D-printed mount for the VL53L0X laser sensors was redesigned to raise the sensor **almost 4 cm above** the previous MDF mount. This height correction directly resolves the finding documented in the Decision Log (**Decision 12/13**): the MDF mount left the sensor with a slight downward tilt, which produced false close-range readings (the sensor "saw" the floor instead of the side wall). With the new geometry, the sensor measures more consistently and reliably.

- **File:** [`SoporteLaser.STL`](cad/SoporteLaser.STL)
- **Material:** 3D printed (PLA)
- **Dimensions (bounding box):** 33.9 × 3.1 × 58.6 mm

> [!NOTE]
> With this part, the mounting fault we documented as "in progress" in the Decision Log (**Decision 12**) is now **resolved** — we updated that entry accordingly.

#### Steering linkage and servo mount

During our tests we found that the connecting hole between the servo and the steering linkage was not centered: it was offset a few millimeters to the left. This did not prevent the robot from working, but it did shift the real wheel angle with respect to the angle the servo reported as center — that is, "servo center" and "wheels aligned" were no longer the same point. We corrected the hole position and, taking advantage of the redesign, we also modified the servo mount to house our new **ST3215-HS** servo (see [Software Architecture](#arquitectura-software) and [Power Architecture](#arquitectura-potencia)).
cad/S25_Soporte_Servo_Rev_8.STL
- **File:** [`R26_EnlaceDireccion_Rev7.STL`](cad/R26_EnlaceDireccion_Rev7.STL)[`S25_Soporte_Servo_Rev_8.STL`](cad/S25_Soporte_Servo_Rev_8.STL)
- **Material:** cut from flat sheet material (the linkage itself is not printed; the accompanying servo mount is 3D printed in PLA)
- **Dimensions (bounding box):** 116.4 × 3.1 × 17.4 mm

#### Steering knuckle

The steering knuckle is the part located at each end of the steering system, connecting the wheel to the steering linkage. It works as a pivot: on one side it holds the wheel axle/bushing, and on the other it articulates both with the chassis (defining the steering axis) and with the steering linkage, which is what receives the movement from the servo. When the servo moves the steering linkage, the linkage pushes or pulls the knuckle, making it rotate about its own pivot — and since the wheel is mounted directly on the knuckle, that rotation translates into the change in wheel angle. In other words, the knuckle is what converts the linear/angular movement of the steering linkage into the wheel's actual turn.

- **File:** [`S25_Mangueta_Rev_2.STL`](cad/S25_Mangueta_Rev_2.STL)
- **Material:** 3D printed (PLA)
- **Dimensions (bounding box):** 14.4 × 31.9 × 21.0 mm

#### Motor mount and transmission

The drive motor's output shaft is coupled to Lego axles, which in turn connect directly to the wheels. A custom mount keeps these axles aligned at **180°** with respect to each other, preventing them from flexing. This matters for two reasons: it avoids generating an additional unwanted force on the motor's Z axis (which would shorten its life and affect power transmission), and it keeps behavior consistent and predictable in every test. This part belongs to the same family as the one we corrected in the Decision Log (**Decision 13**, undersized axle hole causing drift); this revision (**Rev 4B**) is the current version, with the axles correctly aligned and no reported flexing problems.

- **File:** [`S25_Soporte_de_motor_y_transmision_Rev_4B.STL`](cad/S25_Soporte_de_motor_y_transmision_Rev_4B.STL)
- **Material:** 3D printed (PLA)
- **Dimensions (bounding box):** 44.5 × 51.8 × 63.5 mm

#### Platform / upper deck

First deck of the chassis, where part of the robot's electronics is mounted.

- **File:** [`R26_piso_1_rev2.STL`](cad/R26_piso_1_rev2.STL)
- **Dimensions (bounding box):** 114.9 × 3.1 × 26.8 mm

#### Chassis

The chassis was modified at the beginning of this season with the goal of reducing the robot's total size and optimizing the internal layout of the electronic components. As a result, the current chassis is **2 cm shorter and 2 cm narrower** than the previous version.

- **File:** [`S25_chasis_rev18.STL`](cad/S25_chasis_rev18.STL)
- **Dimensions (bounding box):** 177.8 × 3.1 × 135.7 mm

> [!NOTE]
> Confirmed materials: the **steering knuckle**, the **motor mount and transmission**, the **laser sensor mount** and the **servo mount** (part of the "Steering linkage and servo mount" piece) are 3D printed in **PLA**. The **steering linkage** (the bar itself) is cut from flat sheet material, not PLA. The **chassis** and the **platform/deck** have a uniform thickness of 3.1 mm in the file, consistent with flat sheet cutting (MDF/acrylic).

<a id="fotos-del-vehiculo"></a>

### Vehicle Photos (Current State)

<table align="center">
<tr>
<td align="center"><img src="v-photos/frontView.jpeg" width="200"><br><sub>Front View</sub></td>
<td align="center"><img src="v-photos/leftView.jpeg" width="200"><br><sub>Left Side View</sub></td>
<td align="center"><img src="v-photos/rearView.jpeg" width="200"><br><sub>Rear View</sub></td>
</tr>
<tr>
<td align="center"><img src="v-photos/rightView.jpeg" width="200"><br><sub>Right Side View</sub></td>
<td align="center"><img src="v-photos/upperView.jpeg" width="200"><br><sub>Top View</sub></td>
<td align="center"><img src="v-photos/lowerView.jpeg" width="200"><br><sub>Bottom View</sub></td>
</tr>
</table>

[⬆ Back to top](#indicleto)
 
---
 
<a id="arquitectura-potencia"></a>

## 2. Power and Sensor Architecture

> [!NOTE]
> This section details how energy is distributed inside the robot —from the battery to each sensor and actuator— and the current budget that results from that distribution.

### Power topology

The robot is powered by a single **15 V** battery, which first passes through a main **switch** and from there is split between **two step-down regulation branches**: one for control and traction, and another dedicated exclusively to steering.

- **11.1 V regulator:** its output directly powers the **drive motor** (through the TB6612FNG H-bridge) and the **Arduino Mega** through its Vin input. The **5 V** that the Arduino itself regulates internally powers the sensors: the **two laser sensors** (VL53L0X), the **gyro sensor** (MPU9250/GY-9250), the **camera** (HuskyLens) and the **ultrasonic sensor** (HC-SR04P).
- **6 V regulator:** powers, on a dedicated line, the **Open Challenge servo (MG90S)**.
- **7.5 V regulator:** powers, also on a dedicated line, the **Obstacle Challenge servo (Waveshare ST3215-HS)**.

Both servos have their own exclusive supply line (they do not share a regulator with any other component) because we consider the servo to be the main positioning variable for solving the challenges: if it does not have the current available at the exact moment, all the steering accuracy is compromised.

<div align="center">
<img src="schemes/diagrama_topologia_potencia.jpeg" width="620" alt="Power topology diagram">
<br><sub>Power topology: battery → switch → three regulation branches (11.1 V control/traction, 6 V Open Challenge servo, 7.5 V Obstacle Challenge servo).</sub>
</div>

```
15 V battery → Switch ─┬─ 11.1 V regulator ─┬─ Drive motor (via TB6612FNG)
                        │                    └─ Arduino Mega → (5 V) → VL53L0X ×2, GY-9250, HuskyLens, HC-SR04P
                        ├─ 6 V regulator   ──── MG90S servo (Open Challenge, dedicated line)
                        └─ 7.5 V regulator ──── ST3215-HS servo (Obstacle Challenge, dedicated line)
```

### Wiring diagram (pins)

<div align="center">
<img src="schemes/diagrama_conexiones_pines.jpeg" width="680" alt="Arduino Mega wiring and pin diagram">
<br><sub>Connection of each component to the Arduino Mega 2560 pins: PWMA/AIN1/AIN2/STBY (12, 10, 11, 8) to the TB6612 driver; XSHUT (7, 6) and I2C bus (20 SDA, 21 SCL) for the VL53L0X, MPU9250 and HuskyLens; encoder on pin 3; HC-SR04P on TRIG/ECHO (5, 4); Serial1 (18 TX1, 19 RX1) for the ST3215-HS servo; button on pin 23; buzzer on pin 49.</sub>
</div>

### Current budget

| Component | Image | Operating voltage | Current draw |
|---|---|---|---|
| VL53L0X laser sensor | <img src="schemes/laser.jpg" width="80"> | 5 V | ≈ 10 mA (x2) |
| HC-SR04P ultrasonic sensor | <img src="schemes/ULTRASONICO.webp" width="80"> | 5 V | ≈ 15 mA |
| GY-9250 gyro sensor | <img src="schemes/GIRO.jpg" width="80"> | 5 V | ≈ 6.6 mA |
| MG90S servo (Open Challenge) | <img src="schemes/SERVO.webp" width="80"> | 6 V | ≈ 83–417 mA |
| ST3215-HS servo (Obstacle Challenge) | <img src="schemes/ST3215.jpg" width="80"> | 7.5 V | ≈ 100–900 mA |
| GA37-520 drive motor | <img src="schemes/MOTORDC.jpg" width="80"> | 11.1 V | ≈ 500–1500 mA |
| HuskyLens camera | <img src="schemes/HUSKY.webp" width="80"> | 5 V | ≈ 320 mA |
| Arduino Mega 2560 | <img src="schemes/ArduinoMega.jpg" width="80"> | 11.1 V | ≈ 22.5–45 mA |
| **Total (Open Challenge)** | | | **≈ 967 mA – 2.32 A** |
| **Total (Obstacle Challenge)** | | | **≈ 984 mA – 2.80 A** |

> [!NOTE]
> Each current draw was taken from the component's datasheet, referred to the actual voltage at which it operates in this circuit. The totals add up current from different rails (5 V, 6 V/7.5 V and 11.1 V) depending on the challenge; the current the battery actually delivers is lower and depends on the efficiency of each step-down regulator.

[⬆ Back to top](#indicleto)
 
---
 
<a id="arquitectura-software"></a>

## 3. Software Architecture and Obstacle Strategy

> [!NOTE]
> This section explains, in our own words, **how and why** the robot's software works in each challenge: the control architecture, which sensor does what, the actual code running on the Arduino, and the reasoning (and the stumbles) behind each subsystem. It complements the [Engineering Decision Log](#bitacora-decisiones), which keeps the timeline of hardware/algorithm changes at the project level.

<a id="open-challenge-sw"></a>

### 3.1 Open Challenge

In the Open Challenge there are no pillars to dodge, so the problem reduces to completing 3 laps (12 corners in total) while keeping a constant distance to a wall and turning accurately at each corner. That is why this program is simpler than the Obstacle Challenge one: it does not use the color camera, and the steering servo is a standard **MG90S** controlled with the Arduino `Servo` library (unlike the serial bus servo we use in the Obstacle Challenge).

**Hardware used by this program:** MG90S servo (steering), HC-SR04P ultrasonic sensor (front), two lateral VL53L0X (wall following), MPU6050 gyroscope via the `MPU6050_light` library (heading and turns), encoder (final stretch), and the TB6612FNG H-bridge (drive motor).

**Corner detection and turn direction.** The robot moves forward until the front ultrasonic sensor detects a wall at **30 cm or less** (`TD = 30`). At the very first corner of the whole round, and only there, it compares the two lateral VL53L0X readings (`decideSentido()`): the side that sees farther is the inside of the track, and that is the turn direction used for **all 12 corners** of the round, without recomputing it again.

**Straight stretch.** Before the first corner the robot does not yet have a "known" wall to follow, so it drives straight using the gyroscope (`conduceRectoGiro()`: a proportional controller that corrects the steering to hold the heading at 0°). From the second corner onward, each straight stretch is driven with **PID wall following** (`Kp=17, Ki=0.5, Kd=35`, setpoint of **15 cm**, function `Wallfolowing()`), always on the **outer** side of the track — the sensor opposite the turning side — because the outer wall is continuous, while the inner one has openings at every corner.

> [!NOTE]
> **PID gain tuning method:** the three gains were tuned empirically and in this order: first **Kp**, raising it until the robot visibly oscillated against the wall and then backing it off one step; then **Kd**, to damp that oscillation without losing responsiveness; and finally **Ki**, at a low value, only to correct the sustained offset that remained on long stretches. The 15 cm setpoint was chosen because it leaves enough margin both in the narrow corridor (600 mm) and in the wide one (1000 mm) defined by the Open Challenge rules, without bringing the robot too close to the inner wall in the narrow corridor.

**Turning maneuver.** Upon detecting the front wall, the robot closes the turn in a loop with the gyroscope until it reaches a target angle (`Giros()`). These angles were calibrated empirically and are cumulative within each lap (they reset every 4 corners): **60°** at the 1st corner of the lap, **120°** at the 2nd, **182°** at the 3rd and **241°** at the 4th. When it finishes, the robot reduces its speed and keeps following the wall until it counts a certain number of encoder pulses, and then the robot stops.

**Closing the round.** After completing the 12 corners, the robot reduces speed and continues wall following for an additional fixed distance (2555 encoder pulses) before stopping — this final stretch brings it close to the parking zone.

<details>
<summary>📄 View full code — Open Challenge (<code>abierto.ino</code>)</summary>

```cpp
#include <Adafruit_VL53L0X.h>  // ToF sensor Library
#include <Servo.h>             // Servomotor Library
#include <Wire.h>              // I2C Library
#include <MPU6050_light.h>     // MPU Library
MPU6050 mpu(Wire);

// Yaw variables
float yaw = 0.0;
unsigned long lastTime = 0;
float gyroZOffset = 0.0;

// For calibration
bool calibrated = false;
const int CALIBRATION_SAMPLES = 510;

volatile int Contador = 0;

unsigned long Cont = 0;
bool izq = false;

float global = 0;

//PID Wall Follower variables
float P, I, D, error, L_error, Servo_OUT, DeltaError, SumError, PID;
float setpoint = 15;  // Centimeters
float Kp = 17, Ki = 0.5, Kd = 35;
float Ts = 0.5;  // Sampling time for the integral sum

// Motor drive
const int STBY = 8;
const int PWMA = 12;
const int AIN1 = 10;
const int AIN2 = 11;

// Steering servo
Servo SvD;

//Ultrasonic Frontal Sensor
const int triggerPin3 = 5;
const int echoPin3 = 4;
volatile float distanceF;

//ToF Lateral Sensors
#define XSHUT1 6
#define XSHUT2 7
Adafruit_VL53L0X sensor1 = Adafruit_VL53L0X();
Adafruit_VL53L0X sensor2 = Adafruit_VL53L0X();
float medidaD, medidaI;

//Algorithim Variables/Values
float TD = 30;  //Target frontal distance ... CM
bool Turn = true;
bool Orientation = false;
bool Giro = false;
int CV;

int sentidoGiro = 2;     // decided only at the 1st corner: 1=right, 2=left
float Kp_rumbo = 3.0;    // gain for the gyro straight-heading control (1st stretch)

void setup() {
  Serial.begin(115200);
  Wire.begin();

  pinMode(3, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(3), inter, RISING);

  pinMode(triggerPin3, OUTPUT);
  pinMode(echoPin3, INPUT);

  Wire.beginTransmission(0x68);
  Wire.write(0x6B);
  Wire.write(0);
  Wire.endTransmission(true);

  pinMode(STBY, OUTPUT);
  pinMode(PWMA, OUTPUT);
  pinMode(AIN1, OUTPUT);
  pinMode(AIN2, OUTPUT);
  // The motor begins off
  digitalWrite(STBY, LOW);

  pinMode(XSHUT1, OUTPUT);
  pinMode(XSHUT2, OUTPUT);

  SvD.attach(A1);
  delay(50);
  tone(49, 293, 125);
  delay(50);

  SvD.write(105);
  delay(400);

  byte status = mpu.begin();
  while (status != 0) {}

  mpu.calcOffsets();  // Library's internal calibration
  calibrateGyroZ();   // Additional Z-specific calibration

  lastTime = millis();
  calibrated = true;

  tone(49, 1000, 125);

  // Turn off all the sensors
  digitalWrite(XSHUT1, LOW);
  digitalWrite(XSHUT2, LOW);
  delay(10);

  // Power up sensor 1 and give it address 0x30
  digitalWrite(XSHUT1, HIGH);
  delay(10);
  sensor1.begin(0x30);

  // Power up sensor 2 and give it address 0x31
  digitalWrite(XSHUT2, HIGH);
  delay(10);
  sensor2.begin(0x31);
  
}

void loop() {

  while (CV < 12) {
    distanceF = TD + 3;
    SUSF();
    delay(20);

    if (distanceF <= TD) {
      if (CV == 0) decideSentido();     // 1st corner: decides the direction for ALL the laps
      Giros(sentidoGiro, 175, 60);
      tone(49, 290, 75);
      distanceF = TD + 100;
      CV++;
      delay(1);
      if (CV == 4 || CV == 8 || CV == 12) {
        global = 0;
        yaw = 0;
      }
      SvD.write(105);
    } else {
      if (CV == 0) {
        conduceRectoGiro();             // before the 1st corner: drive straight by gyro (no wall following)
        moverMotor(200);
      } else {
        Wallfolowing();
        moverMotor(200);
      }
    }
  }
  if (CV >= 12) {
    moverMotor(120);
    while (Contador < (Cont + 2555)) {
      Wallfolowing();;
    }
    detenerMotores(1000000000000000000000000000000000000000000000000000);
  }
}



// ===== Decides the turn direction at the 1st corner: reads both ToF, turns toward the more open side =====
void decideSentido() {
  VL53L0X_RangingMeasurementData_t m1, m2;
  sensor1.rangingTest(&m1, false);
  sensor2.rangingTest(&m2, false);
  float izq = m1.RangeMilliMeter / 10.0;   // sensor1 = LEFT side (medidaI)
  float der = m2.RangeMilliMeter / 10.0;   // sensor2 = RIGHT side (medidaD)
  Serial.print("izq="); Serial.print(izq);
  Serial.print("  der="); Serial.println(der);
  // The side that reads MORE is the more open one -> turn toward that side.
  // If it decides the other way around on your robot, invert this comparison (change > for <).
  if (izq > der) {
    Orientation = false; sentidoGiro = 2;              // open on the LEFT -> turn left
    tone(49, 1200, 120); delay(160); tone(49, 1200, 120);
  } else {
    Orientation = true;  sentidoGiro = 1;              // open on the RIGHT -> turn right
    tone(49, 700, 250);
  }
  delay(150);
}

// ===== Straight driving by gyro (target heading = 0), without wall following =====
void conduceRectoGiro() {
  GradoZ();                                // updates yaw
  float err = 0 - yaw;                      // holds the initial heading (yaw = 0)
  int salida = 105 + (int)(Kp_rumbo * err);
  salida = constrain(salida, 35, 160);
  SvD.write(salida);
}

void SUSF() {
  digitalWrite(triggerPin3, LOW);
  delayMicroseconds(2);
  digitalWrite(triggerPin3, HIGH);
  delayMicroseconds(10);
  digitalWrite(triggerPin3, LOW);
  long durationF = pulseIn(echoPin3, HIGH);
  distanceF = durationF / 58;
}
void Wallfolowing() {
  // Get the sensor reading.
  if (Orientation == true) {
    VL53L0X_RangingMeasurementData_t medida1;
    sensor1.rangingTest(&medida1, false);
    delay(5);
    medidaI = medida1.RangeMilliMeter / 10;
    error = medidaI - setpoint;
  } else {
    VL53L0X_RangingMeasurementData_t medida2;
    sensor2.rangingTest(&medida2, false);
    delay(5);
    medidaD = medida2.RangeMilliMeter / 10;
    error = medidaD - setpoint;
  }
  DeltaError = error - L_error;
  SumError += L_error;
  P = Kp * error;
  I = Ki * Ts * SumError;
  I = constrain(I, -55, 55);
  D = ((Kd / Ts) * DeltaError);
  PID = P + I + D;
  L_error = error;

  PID = constrain(PID, -10, 10);
  if (Orientation == true) {
    Servo_OUT = 105 - PID;
  } else {
    Servo_OUT = 105 + PID;
  }
  SvD.write(Servo_OUT);
}
void moverMotor(int velocidad) {
  digitalWrite(STBY, HIGH);
  if (velocidad > 0) {
    digitalWrite(AIN1, HIGH);
    digitalWrite(AIN2, LOW);
  } else {
    digitalWrite(AIN1, LOW);
    digitalWrite(AIN2, HIGH);
    velocidad = -velocidad;
  }
  analogWrite(PWMA, constrain(velocidad, 0, 255));
}

void inter() {
  Contador++;
}

void MotorEnc(int velocidad, int pulsos) {
  Contador = 0;
  delay(5);

  digitalWrite(STBY, HIGH);
  if (velocidad > 0) {
    digitalWrite(AIN1, HIGH);
    digitalWrite(AIN2, LOW);
    analogWrite(PWMA, constrain(velocidad, 0, 255));
    while (Contador <= pulsos) {}

  } else {
    digitalWrite(AIN1, LOW);
    digitalWrite(AIN2, HIGH);
    analogWrite(PWMA, constrain(-velocidad, 0, 255));
    while (Contador <= pulsos) {}
  }

  detenerMotores(0);
}

void detenerMotores(int T) {
  analogWrite(PWMA, 0);
  digitalWrite(STBY, LOW);  // Standby to save energy
  delay(T);
}

void calibrateGyroZ() {
  float sum = 0;
  for (int i = 0; i < CALIBRATION_SAMPLES; i++) {
    mpu.update();
    sum += mpu.getGyroZ();
    delay(5);
  }
  gyroZOffset = sum / CALIBRATION_SAMPLES;
  Serial.print("Gyro Z Offset: ");
  Serial.println(gyroZOffset, 6);
}

void GradoZ() {
  mpu.update();

  if (calibrated) {
    unsigned long currentTime = millis();
    float deltaTime = (currentTime - lastTime) / 1000.0;  // Time in seconds
    lastTime = currentTime;

    if (deltaTime > 0.1) deltaTime = 0.01;  // Limit the maximum deltaTime

    // Get the Z angular rate (degrees/second) and apply the offset
    float gyroZRate = mpu.getGyroZ() - gyroZOffset;

    // INTEGRATE to get the angle: angle = angular rate × time
    yaw += gyroZRate * deltaTime;

    // Keep yaw within the 0-360 degree range
    if (yaw <= -360) yaw = 0;
    if (yaw >= 360) yaw = 0;
    Serial.println(yaw);
  }

  delay(12);  // Little Pause for estability
}

void Giros(int d, int V, float G) {
  tone(49, 3520, 50);
  GradoZ();
  float targetYaw;
  if (d == 1) SvD.write(160);
  else { SvD.write(35); }

  delay(50);

  if (CV == 0 || CV == 4 || CV == 8) targetYaw = G;
  else if (CV == 1 || CV == 5 || CV == 9) targetYaw = G * 2;
  else if (CV == 2 || CV == 6 || CV == 10) targetYaw = (G * 3) + 2;
  else if (CV == 3 || CV == 7 || CV == 11) targetYaw = (G * 4) + 1;
  if (d != 1) targetYaw = targetYaw * -1;

  global = targetYaw;

  moverMotor(V);

  while (true) {
    GradoZ();

    if (d == 1) {
      if (yaw >= targetYaw) {
        break;
      }
    } else {
      if (yaw <= targetYaw) {
        break;
      }
    }
  }

  MotorEnc(-115, 13);
  delay(100);

  if (d == 1) dir(160, 105, 10);
  else { dir(35, 105, 10); }

  SvD.write(105);
  delay(50);
  SvD.write(105);
}


void dir(int Vi, int Vf, int T) {
  if (Vi < Vf) {
    for (int pos = Vf; pos <= Vi; pos += 5) {
      SvD.write(pos);
      delay(T);
    }
  } else {
    for (int pos = Vf; pos >= Vi; pos -= 5) {
      SvD.write(pos);
      delay(T);
    }
  }
  SvD.write(105);
}
```

</details>

[⬆ Back to top](#indicleto)

---

<a id="obstacle-challenge-sw"></a>

### 3.2 Obstacle Challenge

<a id="marco-del-reto"></a>

#### 1. Challenge framework and design goals

The WRO Future Engineers 2026 Obstacle Challenge asks the vehicle to drive, fully autonomously, three laps around an eight-section track —four corners and four straights— while avoiding traffic signs placed at random before each round. The rule is simple to state but demanding to satisfy: a **red** pillar forces the robot to pass on its **right** side, a **green** one on the **left**, and under no circumstances may the sign be moved. Since the direction of the round (clockwise or counterclockwise) is decided at random right before the start, the algorithm cannot assume anything up front: it has to figure it out by itself as soon as it starts moving. And at the end, after the three laps, it has to return to the parking lot.

From the beginning we set ourselves three goals when designing the system: that it would cope well with randomness (pillar positions, direction of the round), that it would depend as little as possible on lighting or on exact track measurements, and that it could be debugged piece by piece. That last one ended up being almost the team's golden rule: **measure before prescribing**. Every value that mattered was left as an adjustable constant, and every subsystem was tested in isolation in its own program before being combined with the rest — that way, when something failed, we knew exactly which module to look in.

<a id="maquina-de-estados"></a>

#### 2. General architecture: state machine

The robot's control is organized as a **state machine** that basically mirrors the shape of the track. On each straight stretch, the vehicle goes through four states:

- **STRAIGHT (`RECTO`):** drives forward holding the heading.
- **TRACK (`SEGUIR`):** upon detecting a pillar, keeps it centered in the camera frame while approaching.
- **DODGE (`ESQUIVAR`):** at a certain distance, executes the avoidance maneuver.
- **RETURN (`REGRESAR`):** resumes the straight heading.

On top of all this, the robot can also enter the corner maneuver — but only if it is in the STRAIGHT state. That restriction was a deliberate decision: it guarantees that the robot never tries to take a corner in the middle of a dodge, settling once and for all the conflict between "dodge the pillar" and "take the corner" in favor of the former.

Splitting the control this way let each task use the sensor best suited to it, without them getting in each other's way: the camera handles TRACK and DODGE, the gyroscope takes care of the heading in STRAIGHT and RETURN (and of the corner turn), the distance sensors detect the corners, and the encoder provides odometry for all the maneuvers.

<a id="sensores-obstaculos"></a>

#### 3. Sensor suite and role assignment

On an Arduino Mega, the robot integrates five perception/actuation systems, most of them connected over the same I2C bus:

- **HuskyLens camera** (color recognition mode): detects the pillars and gives their horizontal position in the frame, plus their apparent height in pixels, which we use as an estimator of how close the pillar is (we do not use a dedicated distance sensor for the pillars).
- **Two VL53L0X time-of-flight sensors**, in **long range mode**: detect when a corner opens up and, if needed, help decide whether a forward or a reverse turn is preferable. The mounting height is not arbitrary: the redesigned mount (see [Mechanical Design Parts](#piezas-cad)) raises the sensor almost 4 cm compared with the previous mount, which leaves it aiming within the **100 mm height** band that the rules specify for the track walls (rule 13.3/13.5) — neither so low that it reads the floor, nor so high that it passes over the wall.
- **Gyroscope (GY-9250, via MPU6050):** computes the heading (*yaw*) by integrating the angular rate — it is the basis of both the straight-line control and the turns.
- **Single-channel encoder** on the drive axle: provides the distance travelled for all odometry-based maneuvers.
- **Waveshare ST3215 steering servo:** unlike the Open Challenge (which uses a standard MG90S), here we use a **serial bus** servo (half-duplex protocol over `Serial1`, `SCServo` library), which allows position, speed and acceleration to be set independently. This lets us compute how long the steering takes to reach each angle and **compensate for that delay** when computing the dodge, turn and braking distances (see point 9 below) — something a normal PWM servo would not let us do with the same precision.

Distributing the tasks among specialized sensors, instead of loading everything onto the camera, was something we learned the hard way: the camera already had enough work detecting pillars under variable light, and if we also asked it to distinguish the floor lines, it became unreliable. Separating perception by domain —color to the camera, lateral distance to the ToF sensors, heading to the gyroscope— made the whole system more robust.

<a id="control-rumbo-recto"></a>

#### 4. Straight-heading control

At first we thought it was enough to "leave the steering centered" for the robot to drive straight. It is not: a mechanically centered steering does not guarantee a straight trajectory, and any misalignment —however small— accumulates as drift (in fact, we found a concrete mechanical cause of this; see Decision Log, **Decision 13**). That is why we closed the heading loop with the gyroscope: a proportional controller corrects the servo according to the error between the heading we want (`targetYaw`) and the one the gyroscope is measuring. The servo's movement toward that correction is made gradually (`servoSuave()`, with a limited rate of change — `SERVO_SLEW`), to avoid abrupt jerks in the steering.

<a id="seguimiento-pilares"></a>

#### 5. Pillar tracking (TRACK state)

When a pillar appears with a minimum size in the frame (`TAM_MIN` filter, so as not to chase small blobs or floor lines), the robot enters TRACK and keeps it centered with a **proportional-derivative** controller that adjusts the servo according to how far the pillar is from the camera's real center (`CENTRO_IMG`, already calibrated). The robot moves from TRACK to the dodge maneuver when the **pillar's height in pixels** exceeds a threshold (`H_ESQUIVAR`) — that is, the "it is close enough now" signal comes from the apparent size in the camera, not from a dedicated distance sensor.

- **A rather instructive sign error:** in an early version, the robot **moved away** from the pillar instead of tracking it. From the outside it looked like it was dodging the box, and when it lost sight of it, it straightened out on its own — it looked exactly like a dodge, but it was really the tracker correcting backwards. Simply inverting the sign of the camera loop was enough for it to start converging correctly toward the center.
- If the robot loses sight of the pillar for several consecutive frames (`FRAMES_PERDIDO`) without having reached the dodge, it returns to the STRAIGHT state.

<a id="decision-sorteo"></a>

#### 6. Dodge decision: color voting and a committed maneuver (DODGE state)

This phase concentrated two of the most important decisions in the whole project, and both came out of failures we saw on the track and had to correct.

**Color voting during the approach.** At first, the pillar's color was decided right at the moment the robot reached the dodge distance. The problem is that, at point-blank range, the pillar fills the whole camera frame and the color classification becomes noisy. The solution was to accumulate color "votes" throughout the approach (`votosRojo`, `votosVerde`) —when the pillar is still seen at medium distance and the color is reliable— and decide by majority right before dodging. That way, a single bad reading near the pillar no longer ruins the decision.

**Committing to a direction, regardless of position.** The subtlest failure of all was that the dodge followed the pillar's *position* in the frame instead of committing to a fixed direction: if the pillar came in slightly biased toward one side, the steering started off toward that same side, and we ended up dodging on the wrong side according to where the pillar was, not according to its color. We fixed it by turning this phase into a **binary decision that is blind to position** (`decideEsquive()`): if it detects red, the steering commits to the **right**; if green, to the **left**; and if it cannot identify any valid color, an alarm sounds. Once the decision is made, the steering stays fixed for the rest of the maneuver, **without looking at the pillar's position again** — this completely eliminated the wrong-side dodges.

The maneuver ends by distance travelled (odometry), by a maximum angle with respect to the heading (`MAX_DODGE_ANGLE`, to keep the robot from spinning around) or by a safety time limit (`MAX_ESQUIVE_MS`); after that, it moves to RETURN, resumes the heading with `conduceRecto()` until it is aligned within a tolerance (`TOL_RUMBO`) and goes back to STRAIGHT.

<a id="deteccion-esquinas"></a>

#### 7. Corner detection

Corner detection is based on something simple: when the wall on one side disappears, the sensor on that side stops reading a short distance and starts reading "open" (`LADO_LIBRE_CM`). This detection is **only armed in the STRAIGHT state**, and only after the robot has moved far enough away from the previous corner (`REARM_CM`), so that it does not trigger twice on the same corner. In practice, this was the subsystem that gave us the most trouble.

- **How the robot decides which side the corners are on:** since the direction of the round is random, the robot works it out by itself at start-up: it hugs the outer barrier and averages several readings from both ToF sensors; the sensor that sees **farther** points toward the inside of the track, which is exactly the side where the corners will open up. That side (`esquinaIzq`) is fixed for the whole round, and that is how we resolved the clockwise/counterclockwise ambiguity in one go, without needing to read any floor line.
- **The sensor's range cost us time:** during testing the detection was intermittent — sometimes it worked, sometimes it did not, sometimes it was late. On investigating we found that the VL53L0X, in its default mode, is only reliable up to about **50 cm** — well below the more than **100 cm** needed to distinguish a corner. That explained why the same sensor worked perfectly following a wall up close in the Open Challenge, but failed to detect the opening. The solution was to enable **long range mode** when initializing the sensor, and to confirm the opening with **several consecutive readings** (`PROT_TOF`) before accepting it, in order to discard odd spikes.
- **And at one point, the problem was mechanical:** even with the correct mode, detection sometimes failed on the track because the sensor on the open side reported a few centimeters where it should have read "open". We found that the sensors were slightly tilted toward the floor — to the naked eye they looked perpendicular, but the numbers said otherwise. **This is the same type of problem we documented in the Decision Log, Decision 12/13** (misaligned lateral sensor mounts).

<a id="giro-esquina"></a>

#### 8. Corner turn

Once the opening is armed and confirmed, the robot enters "approaching the corner" mode: it keeps driving straight (with `conduceRecto()`) while deciding, sensor in hand, **how and when** to execute the turn.

- **Forward or reverse turn.** On confirming the corner, the robot measures the distance to the **outer** wall (the side opposite the corner). If that wall is farther away than normal (`DIST_REVERSA_CM`), the robot takes the corner **in reverse** (`giraEsquinaReversa()`: steering to the opposite side, motor backwards, its own target angle) instead of the normal forward turn (`giraEsquina()`). The logic: when there is more free space with respect to the outer wall, turning in reverse gives the robot a tighter effective turning radius to take the corner, instead of needing the extra space a forward turn demands.
- **What triggers the exact moment to turn.** While approaching the vertex, the robot checks the **front ultrasonic sensor**: if it detects the wall within a computed distance (which already includes the servo's own delay — see point 9), it starts the turn. This is the **primary** method; if the ultrasonic sensor does not end up triggering it, there is a safety cap by distance travelled (encoder) that forces the turn anyway, so the robot never keeps driving indefinitely toward a wall the ultrasonic sensor did not detect in time.
- **The gyroscope turn, and the headache of signs.** The turn is closed in a loop with the gyroscope, and here we went through the hardest debugging of the whole project: the steering had to physically turn toward one side while the gyroscope confirmed that same rotation, and both things had to match. The definitive solution was to base the end of the turn on the **absolute change** of heading (the difference, in absolute value, between the current and the initial yaw), regardless of sign. This decision eliminated at the root a whole category of sign errors that had cost us a great many tests.
- **Backing up after the turn.** After finishing the turn, the robot backs up a little to re-center itself before continuing. This distance can be **fixed** (18 cm after a normal turn, 13 cm after a reverse one) or, if adaptive mode is enabled, computed according to how far the outer wall ended up after the turn — this second mode is implemented but currently **disabled** in the code.
- **Local heading per corner.** After each turn, the target heading is reset to 0° — that is, each new edge "starts from zero" instead of accumulating the heading over the whole lap, which prevents the gyroscope's drift from building up over the three laps.

<a id="auto-ajuste-servo"></a>

#### 9. Self-adjustment from the servo profile

Since the Obstacle Challenge steering servo (ST3215) allows speed and acceleration to be set, the code includes a small model of the steering: from the servo's datasheet RPM and the actual voltage we feed it, it computes how many degrees per second it really turns, and from that, how long (in milliseconds) it takes to reach any angle. That delay is then translated into **centimeters the robot has already travelled while the steering was still moving**, and that distance is added to the dodge, corner detection and braking thresholds — so if we change the servo in the future, we only need to update three constants (datasheet RPM, datasheet voltage, actual voltage) and the rest of the distances recompute themselves. The program also warns through the buzzer if, with the mounted servo, the robot is going too fast for the steering's reaction time.

<a id="ingenieria-defensiva"></a>

#### 10. Defensive engineering: safety timeouts and bus protection

A principle we adopted after several lock-up episodes is that **no phase should be able to get stuck indefinitely**. Accordingly, the turn, the dodge and the recovery all have **safety timers** that guarantee an exit even if the sensor that normally ends the phase fails. Likewise, a **timeout was set on the I2C bus**: if a shared device stops responding, the communication cuts the wait short instead of freezing the whole program. The HuskyLens camera also has its own check at start-up (several connection attempts before giving up and warning through the buzzer). These safeguards do not replace fixing the underlying cause, but they turn a catastrophic failure (a stopped vehicle or one spinning out of control) into a bounded, recoverable degradation.

<a id="odometria"></a>

#### 11. Odometry and counter separation

Odometry was calibrated by empirically measuring the encoder's pulses per centimeter. One design detail that emerged from integration was the need for **two independent distance counters**: one for the per-phase distances (dodge, approach to the corner, turn), which resets at every transition, and another for the distance accumulated since the last corner, which governs the re-arming of the detection and which must **not** be reset when a dodge occurs. Without this separation, a dodge in the middle of an edge would have erased the re-arming count and blocked the detection of the next corner.

<a id="metodologia-decisiones-revertidas"></a>

#### 12. Development methodology and reversed decisions

Development followed a **layered integration** strategy: each subsystem (tracking, dodging, heading, corners, exiting the parking lot) was validated in isolation in its own program before being merged into the whole, so that debugging never faced two unknowns at once.

This method proved its worth repeatedly, especially in telling real failures apart from test artifacts: several "wrong" behaviors observed with the vehicle suspended in the air turned out to be the unavoidable consequence of the wheels spinning without the chassis rotating (a test artifact analogous to the one described in the Decision Log, **Decision 13**); the correct test had to be done on the floor.

For the record, the main **decisions reversed or replaced** during the development of this algorithm were:

| Original decision | Replaced by | Reason |
|---|---|---|
| Turn triggered exclusively by odometry (encoder) | Trigger by front ultrasonic sensor, with an encoder safety cap | The encoder alone did not accurately distinguish the exact moment to turn across different approaches; the front ultrasonic sensor, with its threshold already compensated for the servo delay, is more precise, and the encoder remains as a safety backup |
| VL53L0X default mode | Long range mode (~2 m) | Default range (~50 cm) insufficient for detecting corners |
| Point-blank color determination | Color voting during the approach | Classification noise at short range |
| Dodge guided by the pillar's position | Binary decision committed by color | Dodges on the wrong side according to position, not color |
| End of turn based on the sign of yaw | End of turn based on the absolute change of heading | Sign errors that prevented the turn from completing |

Each of these reversals originated in a concrete observation of a failure and was solved by attacking the **root cause** instead of the symptom, in line with the engineering philosophy that guided the whole project.

<details>
<summary>📄 View full code — Obstacle Challenge (<code>obstaculos.ino</code>)</summary>

```cpp
#include <Adafruit_VL53L0X.h>
#include <SCServo.h>
#include <Wire.h>
#include <MPU6050_light.h>
#include "HUSKYLENS.h"

MPU6050 mpu(Wire);
HUSKYLENS camara;
Adafruit_VL53L0X tofIzq = Adafruit_VL53L0X();   // LEFT   (0x31)
Adafruit_VL53L0X tofDer = Adafruit_VL53L0X();   // RIGHT  (0x30)

struct Blob { bool visto; int color; int x; int w; int h; };

// ---- Pins ----
const int STBY = 8, PWMA = 12, AIN1 = 10, AIN2 = 11;
const int ENC_PIN = 3;
const int XSHUT_IZQ = 7, XSHUT_DER = 6;
const int BUZZER = 49;
const int BOTON = 23;
const int TRIG_US = 5, ECHO_US = 4;

// ---- Steering servo: Waveshare ST3215 (TTL on Serial1, pins 18/19) ----
SMS_STS st;
const int SERVO_ID   = 1;
int   VEL_SERVO  = 4095;   // servo speed (max 4095)
int   ACEL_SERVO = 150;    // acceleration (0-254)
const bool INVERTIR_DIR = true;   // the ST3215 turns the OPPOSITE way to the MG90S
// NOTE: MIN must ALWAYS be lower than MAX or constrain() breaks
const int SERVO_CENTRO = 90, SERVO_MIN = 0, SERVO_MAX = 180;

int gradosApasos(float g) { return (int)(g * 4096.0 / 360.0 + 0.5); }
void mueveDir(float grados) {
  float g = INVERTIR_DIR ? (2.0 * SERVO_CENTRO - grados) : grados;
  st.WritePosEx(SERVO_ID, gradosApasos(g), VEL_SERVO, ACEL_SERVO);
}

// ========= SERVO PROFILE: the ONLY thing you change when swapping servos =========
float SERVO_RPM_SPEC = 52.0;   // datasheet RPM. C001=52 | C046=110 | MG90S=110
float SERVO_V_SPEC   = 7.4;    // voltage at which that RPM applies (datasheet)
float SERVO_V_REAL   = 7.5;    // voltage YOU actually feed it

// ========= ROBOT PROFILE =========
float VEL_CM_S_REF = 29.0;     // cm/s MEASURED at PWM_REF (measured with the 100 cm sketch)
int   PWM_REF      = 80;
bool  MIDE_VELOCIDAD = false;  // true = runs the speed measurement and stops

// Fraction of speed actually requested from the ST3215
float fracVelServo() { return constrain((float)VEL_SERVO / 4095.0, 0.05, 1.0); }
// Effective angular speed of the steering (degrees/second)
float gradosPorSeg() { return SERVO_RPM_SPEC * (SERVO_V_REAL / SERVO_V_SPEC) * 6.0 * fracVelServo(); }
// Time (ms) the steering takes to sweep 'grados' (+30 ms of start-up)
unsigned long msServo(float grados) {
  return (unsigned long)((fabs(grados) / gradosPorSeg()) * 1000.0) + 30;
}
// Robot speed (cm/s) at a given PWM
float velRobot(int pwm) { return VEL_CM_S_REF * (float)pwm / (float)PWM_REF; }
// Distance (cm) the robot travels WHILE the steering reaches the angle
float lagCm(float grados, int pwm) { return velRobot(pwm) * msServo(grados) / 1000.0; }

// =================== CALIBRATE ===================
const int ID_ROJO = 1, ID_ROJO_2 = 3;
const int ID_VERDE = 2, ID_VERDE_2 = 4;
bool esRojo(int id)  { return id == ID_ROJO  || id == ID_ROJO_2;  }
bool esVerde(int id) { return id == ID_VERDE || id == ID_VERDE_2; }
int   CENTRO_IMG = 141;
int   SIGNO_CAM  = -1;
float Kp_seguir  = 0.6;
float Kd_seguir  = 1;
int   TAM_MIN    = 30;
int   MAX_ESQUIVE_STEER = 25;

// -- Dodge --
int   H_ESQUIVAR = 67;
int   ESQUIVE_STEER = 45;
float MAX_DODGE_ANGLE = 45;
int   FRAMES_PERDIDO = 4;
float MAX_ESQUIVE_MS = 4000;
float MAX_REGRESO_MS = 4000;

// -- Heading / return --
int   SIGNO_RUMBO = 1;
float Kp_head     = 2.0;
float TOL_RUMBO   = 1;   // DO NOT set to 0: fabs()<0 is never true and blocks the exit from REGRESAR
int   SERVO_SLEW  = 6;

// -- Corners --
float LADO_LIBRE_CM  = 180;
int   PROT_TOF       = 3;
float AVANCE_CORNER_CM = 80;   // safety CAP by encoder (normal turn)
float REARM_CM = 140;
float RETROCESO_POST_GIRO_CM = 18;
bool  CORNER_POR_ULTRASONICO = true;

// -- Adaptive backing up --
bool  RETRO_ADAPTATIVO = false;
float DIST_MURO_REF = 28;
float K_RETRO       = 0.8;
float RETRO_MAX_CM  = 25;

// -- REVERSE turn --
bool  GIRO_REVERSA_ON = true;
float DIST_REVERSA_CM = 80;
float AVANCE_MAX_REVERSA_CM = 130;   // safety CAP by encoder (reverse turn)
float RETROCESO_POST_REVERSA_CM = 13;
float ANGULO_GIRO_REVERSA = 86;      // manual: depends on inertia, NOT on the servo

float ANGULO_GIRO = 88;              // manual: depends on inertia, NOT on the servo
int   VEL_GIRO    = 60;
int   VEL_ESPACIO = 110;
int   PULSOS_ESPACIO = 13;
float MAX_GIRO_MS = 3000;

// -- Start-up --
long  ROT_RETROCESO_INICIAL = 400;
int   VEL_RETROCESO_INI = 80;

// -- Odometry / speed / goal --
float PULSOS_POR_CM = 20.0;
int   VEL       = 80;
int   VEL_RECUP = 75;
int   TOTAL_GIROS = 12;

// ===== BASE DISTANCES (value if the servo were instantaneous) =====
// These are NOT touched when swapping servos: the lag is added automatically.
float ESQUIVE_CM_BASE           = 25;
float DIST_FRONTAL_CORNER_BASE  = 55;
float DIST_FRONTAL_REVERSA_BASE = 15;
float DIST_RECUP_BASE           = 8;

// ===== EFFECTIVE DISTANCES (recomputed automatically for the mounted servo) =====
float esquiveCm()          { return ESQUIVE_CM_BASE           + lagCm(ESQUIVE_STEER, VEL); }
float distFrontalCorner()  { return DIST_FRONTAL_CORNER_BASE  + lagCm(SERVO_MAX - SERVO_CENTRO, VEL); }
float distFrontalReversa() { return DIST_FRONTAL_REVERSA_BASE + lagCm(SERVO_CENTRO - SERVO_MIN, VEL); }
float distRecupCm()        { return DIST_RECUP_BASE           + lagCm(ESQUIVE_STEER, VEL_RECUP); }
// ===============================================

// --------- State ---------
volatile long encCount = 0;
volatile long encCorner = 0;
float yaw = 0, gyroZoffset = 0, targetYaw = 0;
unsigned long lastYawUs = 0, lastCamMs = 0, lastTofMs = 0, lastLog = 0;
float lastI = 999, lastD = 999, ladoEsqCm = 999;
int servoPos = SERVO_CENTRO;
int perdidos = 0, giros = 0, cntEsq = 0;
int colorEsquive = ID_ROJO, servoDodge = SERVO_CENTRO;
int votosRojo = 0, votosVerde = 0;
float errPrev = 0;
unsigned long tFase = 0;
bool avanzandoGiro = false, esquinaIzq = true;
bool giroEnReversa = false;

enum Fase { RECTO, SEGUIR, ESQUIVAR, REGRESAR };
Fase fase = RECTO;

Blob blobActual = {false, 0, 0, 0, 0};

void onEnc() { encCount++; encCorner++; }

void setup() {
  Serial.begin(115200);
  Wire.begin();
  pinMode(BOTON, INPUT_PULLUP);
  Wire.setWireTimeout(3000, true);
  pinMode(ENC_PIN, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(ENC_PIN), onEnc, RISING);
  pinMode(STBY, OUTPUT); pinMode(PWMA, OUTPUT); pinMode(AIN1, OUTPUT); pinMode(AIN2, OUTPUT);
  digitalWrite(STBY, LOW);
  pinMode(XSHUT_IZQ, OUTPUT); pinMode(XSHUT_DER, OUTPUT);
  pinMode(TRIG_US, OUTPUT); pinMode(ECHO_US, INPUT);

  // ---- ST3215 ----
  Serial1.begin(1000000);
  st.pSerial = &Serial1;
  delay(500);
  if (st.Ping(SERVO_ID) == -1) {
    Serial.println(F("ST3215 no responde. Revisa cables / alimentacion / ID."));
    tone(BUZZER, 200, 400);
  }
  mueveDir(SERVO_CENTRO); servoPos = SERVO_CENTRO;
  delay(msServo(90));

  // ---- Speed measurement (optional) ----
  if (MIDE_VELOCIDAD) {
    Serial.println(F("MIDIENDO VELOCIDAD: 3 s a VEL. Deja pista libre."));
    delay(2000);
    resetDist(); motor(VEL); delay(3000); motor(0);
    Serial.print(F("VEL_CM_S medida = ")); Serial.println(distanciaCm() / 3.0, 1);
    Serial.println(F("-> ponla en VEL_CM_S_REF y regresa MIDE_VELOCIDAD a false"));
    while (true) {}
  }

  // ---- Self-adjustment report ----
  Serial.println(F("===== AUTO-AJUSTE ====="));
  Serial.print(F("servo: ")); Serial.print(SERVO_RPM_SPEC, 0); Serial.print(F(" RPM -> "));
  Serial.print(gradosPorSeg(), 0); Serial.println(F(" deg/s"));
  Serial.print(F("robot: ")); Serial.print(velRobot(VEL), 1); Serial.println(F(" cm/s"));
  Serial.print(F("esquive=")); Serial.print(esquiveCm(), 1);
  Serial.print(F("  frontalCorner=")); Serial.print(distFrontalCorner(), 1);
  Serial.print(F("  frontalRev=")); Serial.print(distFrontalReversa(), 1);
  Serial.print(F("  recup=")); Serial.println(distRecupCm(), 1);
  if (lagCm(ESQUIVE_STEER, VEL) > ESQUIVE_CM_BASE * 0.6) {
    Serial.println(F("AVISO: el robot va rapido para este servo. Baja VEL o usa servo mas rapido."));
    tone(BUZZER, 300, 400); delay(500);
  }
  Serial.println(F("======================="));

  byte mpuStatus = mpu.begin(); while (mpuStatus != 0) { mpuStatus = mpu.begin(); }
  mpu.calcOffsets();
  calibraGiroZ();
  lastYawUs = micros();

  // ToF in LONG RANGE mode
  digitalWrite(XSHUT_IZQ, LOW); digitalWrite(XSHUT_DER, LOW); delay(10);
  digitalWrite(XSHUT_DER, HIGH); delay(10);
  tofDer.begin(0x30, false, &Wire, Adafruit_VL53L0X::VL53L0X_SENSE_LONG_RANGE);
  digitalWrite(XSHUT_IZQ, HIGH); delay(10);
  tofIzq.begin(0x31, false, &Wire, Adafruit_VL53L0X::VL53L0X_SENSE_LONG_RANGE);

  // Camera
  while (!camara.begin(Wire)) {
    Serial.println(F("No conecta la HuskyLens (Protocol Type = I2C)"));
    delay(200);
  }
  delay(200);
  camara.writeAlgorithm(ALGORITHM_COLOR_RECOGNITION);
  delay(200);
  camara.writeAlgorithm(ALGORITHM_COLOR_RECOGNITION);
  bool camOK = false;
  for (int i = 0; i < 40; i++) { if (camara.request()) { camOK = true; break; } delay(50); }
  if (!camOK) { while (true) { tone(BUZZER, 200, 300); delay(600); } }
  tone(BUZZER, 1500, 100); delay(130); tone(BUZZER, 1500, 100);

  yaw = 0; targetYaw = 0;
  delay(400);

  // ---- Corner side: whichever sensor sees farther at start-up ----
  float sumI = 0, sumD = 0;
  for (int i = 0; i < 10; i++) { leeTof(); sumI += lastI; sumD += lastD; delay(30); }
  esquinaIzq = (sumI >= sumD);
  Serial.print(F("izq_prom=")); Serial.print(sumI / 10, 0);
  Serial.print(F("  der_prom=")); Serial.print(sumD / 10, 0);
  Serial.print(F("  -> LADO DE ESQUINA = "));
  Serial.println(esquinaIzq ? F("IZQUIERDA") : F("DERECHA"));
  if (esquinaIzq) { tone(BUZZER, 1200, 120); delay(180); tone(BUZZER, 1200, 120); }
  else            { tone(BUZZER, 700, 250); }
  delay(600);

  yaw = 0; targetYaw = 0;

  // ---- Wait for the start button ----
  Serial.println(F("Listo. Esperando boton de arranque..."));
  while (digitalRead(BOTON) == LOW) { delay(10); }
  delay(50);
  tone(BUZZER, 1500, 200);
  yaw = 0; targetYaw = 0;

  MotorEncPulsos(-VEL_RETROCESO_INI, ROT_RETROCESO_INICIAL);
  resetDist(); resetCorner();
}

void loop() {
  actualizaYaw();

  if (millis() - lastCamMs > 33) { blobActual = leeCamara(); lastCamMs = millis(); }
  Blob b = blobActual;

  // phase: 0=RECTO 1=SEGUIR 2=ESQUIVAR 3=REGRESAR 9=turning
  if (millis() - lastLog > 300) {
    lastLog = millis();
    Serial.print(F("fase=")); Serial.print(avanzandoGiro ? 9 : (int)fase);
    Serial.print(F("  ID=")); Serial.print(b.visto ? b.color : -1);
    Serial.print(F("  h=")); Serial.print(b.visto ? b.h : -1);
    Serial.print(F("  ladoEsq=")); Serial.print(ladoEsqCm, 0);
    Serial.print(F("  yaw=")); Serial.print(yaw, 1);
    Serial.print(F("  dist=")); Serial.print(distanciaCm(), 0);
    Serial.print(F("  giros=")); Serial.println(giros);
  }

  // ===== CORNER in progress: drive forward and turn (highest priority) =====
  if (avanzandoGiro) {
    conduceRecto();
    motor(VEL);
    bool listoParaGirar = false;
    bool porUltrasonico = false;
    if (giroEnReversa) {
      float dFrontal = leeUltrasonico();
      if (dFrontal <= distFrontalReversa())            { listoParaGirar = true; porUltrasonico = true; }
      else if (distanciaCm() >= AVANCE_MAX_REVERSA_CM)   listoParaGirar = true;
    } else if (CORNER_POR_ULTRASONICO) {
      float dFrontal = leeUltrasonico();
      if (dFrontal <= distFrontalCorner())             { listoParaGirar = true; porUltrasonico = true; }
      else if (distanciaCm() >= AVANCE_CORNER_CM)        listoParaGirar = true;
    } else {
      listoParaGirar = (distanciaCm() >= AVANCE_CORNER_CM);
    }
    if (listoParaGirar) {
      if (porUltrasonico) {
        if (giroEnReversa) { tone(BUZZER, 2200, 60); delay(70); tone(BUZZER, 2200, 60); }
        else               { tone(BUZZER, 1800, 90); }
      } else {
        tone(BUZZER, 350, 200);   // encoder cap: the ultrasonic sensor did NOT see the wall
      }
      if (giroEnReversa) giraEsquinaReversa(esquinaIzq);
      else               giraEsquina(esquinaIzq);
      giros++;
      avanzandoGiro = false;
      resetDist(); resetCorner();
      if (giros >= TOTAL_GIROS) { freno(); tone(BUZZER, 1500, 500); while (true) {} }
    }
    return;
  }

  // ===== Corner detection: ONLY in RECTO =====
  if (fase == RECTO && millis() - lastTofMs > 50) {
    lastTofMs = millis();
    ladoEsqCm = leeTofEsquina();
    if (giros == 0 || distDesdeCorner() >= REARM_CM) {
      if (ladoEsqCm >= LADO_LIBRE_CM) cntEsq++; else cntEsq = 0;
      if (cntEsq >= PROT_TOF) {
        avanzandoGiro = true; resetDist(); cntEsq = 0;
        float dMuroDet = leeTofExterno();
        giroEnReversa = (GIRO_REVERSA_ON && dMuroDet > DIST_REVERSA_CM);
        Serial.print(F(">> esquina ")); Serial.print(esquinaIzq ? F("IZQ") : F("DER"));
        Serial.print(F("  muroExt=")); Serial.print(dMuroDet, 0);
        Serial.println(giroEnReversa ? F("  -> GIRO EN REVERSA") : F("  -> giro normal"));
        tone(BUZZER, giroEnReversa ? 1400 : 880, 120);
        return;
      }
    } else cntEsq = 0;
  }

  // ===== Dodge state machine =====
  switch (fase) {

    case RECTO:
      conduceRecto();
      motor(VEL);
      if (b.visto) { fase = SEGUIR; perdidos = 0; votosRojo = 0; votosVerde = 0; errPrev = 0; }
      break;

    case SEGUIR:
      if (b.visto) {
        seguidor(b);
        if (esRojo(b.color)) votosRojo++; else if (esVerde(b.color)) votosVerde++;
        perdidos = 0;
        if (b.h > H_ESQUIVAR) {
          colorEsquive = (votosRojo >= votosVerde) ? ID_ROJO : ID_VERDE;
          decideEsquive();
          fase = ESQUIVAR; resetDist(); tFase = millis();
        }
      } else {
        perdidos++;
        mueveDir(servoPos);
        if (perdidos >= FRAMES_PERDIDO) fase = RECTO;
      }
      motor(VEL);
      break;

    case ESQUIVAR:
      servoSuave(servoDodge);
      motor(VEL);
      if (distanciaCm() >= esquiveCm() || fabs(yaw - targetYaw) > MAX_DODGE_ANGLE
          || millis() - tFase > MAX_ESQUIVE_MS) {
        Serial.print(F("<< fin esquive dist=")); Serial.println(distanciaCm());
        fase = REGRESAR; resetDist(); tFase = millis();
      }
      break;

    case REGRESAR:
      
      conduceRecto();
      motor(VEL_RECUP);
      if ((distanciaCm() >= distRecupCm() && fabs(targetYaw - yaw) < TOL_RUMBO)
          || millis() - tFase > MAX_REGRESO_MS) {
        fase = RECTO;
      }
      break;
  }
}

// ===== Binary decision =====
void decideEsquive() {
  if (colorEsquive == ID_ROJO) {
    servoDodge = SERVO_CENTRO + ESQUIVE_STEER;      // red -> RIGHT
    Serial.print(F(">> ESQUIVA ROJO (derecha)"));
    tone(BUZZER, 400, 80);
  } else if (colorEsquive == ID_VERDE) {
    servoDodge = SERVO_CENTRO - ESQUIVE_STEER;      // green -> LEFT
    Serial.print(F(">> ESQUIVA VERDE (izquierda)"));
    tone(BUZZER, 700, 80);
  } else {
    servoDodge = SERVO_CENTRO;
    Serial.print(F(">> COLOR DESCONOCIDO"));
    tone(BUZZER, 4000, 250);
  }
  Serial.print(F("  [votos R=")); Serial.print(votosRojo);
  Serial.print(F(" V=")); Serial.print(votosVerde); Serial.println(F("]"));
}

// ===== Normal turn (measures the ABSOLUTE change in yaw: sign-proof) =====
void giraEsquina(bool haciaIzq) {
  MotorEncPulsos(-VEL_ESPACIO, PULSOS_ESPACIO);
  // If it turns to the wrong side, invert ONLY this line (SERVO_MAX <-> SERVO_MIN)
  int lock = haciaIzq ? SERVO_MAX : SERVO_MIN;
  mueveDir(lock); servoPos = lock;
  delay(msServo(fabs(lock - SERVO_CENTRO)));           // EXACT wait according to the mounted servo
  float yawStart = yaw;
  motor(VEL_GIRO);
  unsigned long t0 = millis();
  while (true) {
    actualizaYaw();
    if (fabs(yaw - yawStart) >= ANGULO_GIRO) break;
    if (millis() - t0 > MAX_GIRO_MS) { Serial.println(F("   [timeout giro]")); break; }
  }
  motor(0);
  targetYaw = yaw;

  mueveDir(SERVO_CENTRO); servoPos = SERVO_CENTRO;
  delay(msServo(fabs(lock - SERVO_CENTRO)));

  if (RETRO_ADAPTATIVO) {
    float dMuro = leeTofExterno();
    float retro = (DIST_MURO_REF - dMuro) * K_RETRO;
    retro = constrain(retro, 0, RETRO_MAX_CM);
    Serial.print(F("   muroExt=")); Serial.print(dMuro, 0);
    Serial.print(F("  retro=")); Serial.println(retro, 0);
    if (retro > 1) retrocedeCentrado(retro);
  } else {
    if (RETROCESO_POST_GIRO_CM > 0) retrocedeCentrado(RETROCESO_POST_GIRO_CM);
  }

  // LOCAL heading: the new edge starts at 0
  yaw = 0; targetYaw = 0;
}

// ===== REVERSE turn: steering to the OPPOSITE side and motor in reverse =====
void giraEsquinaReversa(bool haciaIzq) {
  int lock = haciaIzq ? SERVO_MIN : SERVO_MAX;
  mueveDir(lock); servoPos = lock;
  delay(msServo(fabs(lock - SERVO_CENTRO)));
  float yawStart = yaw;
  motor(-VEL_GIRO);                                    // <-- REVERSE
  unsigned long t0 = millis();
  while (true) {
    actualizaYaw();
    if (fabs(yaw - yawStart) >= ANGULO_GIRO_REVERSA) break;
    if (millis() - t0 > MAX_GIRO_MS) { Serial.println(F("   [timeout giro reversa]")); break; }
  }
  motor(0);
  targetYaw = yaw;

  mueveDir(SERVO_CENTRO); servoPos = SERVO_CENTRO;
  delay(msServo(fabs(lock - SERVO_CENTRO)));

  if (RETROCESO_POST_REVERSA_CM > 0) retrocedeCentrado(RETROCESO_POST_REVERSA_CM);

  yaw = 0; targetYaw = 0;
}

// Backs up a given distance with the steering locked at center.
void retrocedeCentrado(float cm) {
  mueveDir(SERVO_CENTRO); servoPos = SERVO_CENTRO;
  delay(msServo(90));
  resetDist();
  motor(-VEL_RETROCESO_INI);
  unsigned long t0 = millis();
  while (distanciaCm() < cm && millis() - t0 < 2000) { }
  motor(0);
  mueveDir(SERVO_CENTRO); servoPos = SERVO_CENTRO;
  delay(80);
}

void MotorEncPulsos(int velocidad, int pulsos) {
  resetDist();
  motor(velocidad);
  unsigned long t0 = millis();
  while (encCount <= pulsos && millis() - t0 < 1000) {}
  motor(0);
}

// ===== Tracker: brings the object to the center (PD) =====
void seguidor(Blob b) {
  float err = CENTRO_IMG - b.x;
  float d = err - errPrev;
  float salida = Kp_seguir * err + Kd_seguir * d;
  errPrev = err;
  salida = constrain(salida, -MAX_ESQUIVE_STEER, MAX_ESQUIVE_STEER);
  int servo = SERVO_CENTRO + SIGNO_CAM * (int)salida;
  mueveDir(servo); servoPos = servo;
}

// ===== Camera =====
Blob leeCamara() {
  Blob b = {false, 0, 0, 0, 0};
  if (!camara.request()) return b;
  long mejorArea = -1;
  while (camara.available()) {
    HUSKYLENSResult r = camara.read();
    if (r.command != COMMAND_RETURN_BLOCK) continue;
    int w = r.width, h = r.height;
    if (!esRojo(r.ID) && !esVerde(r.ID)) continue;
    if (min(w, h) < TAM_MIN) continue;
    long area = (long)w * h;
    if (area > mejorArea) {
      mejorArea = area;
      b.visto = true; b.color = r.ID; b.x = r.xCenter; b.w = w; b.h = h;
    }
  }
  return b;
}

// ===== Straight heading =====
void conduceRecto() {
  float err = targetYaw - yaw;
  int salida = SERVO_CENTRO + SIGNO_RUMBO * (int)(Kp_head * err);
  salida = constrain(salida, SERVO_MIN, SERVO_MAX);
  servoSuave(salida);
}
void servoSuave(int objetivo) {
  objetivo = constrain(objetivo, SERVO_MIN, SERVO_MAX);
  if (objetivo > servoPos)      servoPos = min(servoPos + SERVO_SLEW, objetivo);
  else if (objetivo < servoPos) servoPos = max(servoPos - SERVO_SLEW, objetivo);
  mueveDir(servoPos);
}

void actualizaYaw() {
  mpu.update();
  unsigned long now = micros();
  float dt = (now - lastYawUs) / 1000000.0;
  lastYawUs = now;
  if (dt < 0 || dt > 0.2) dt = 0;
  yaw += (mpu.getGyroZ() - gyroZoffset) * dt;
}
void calibraGiroZ() {
  float s = 0;
  for (int i = 0; i < 500; i++) { mpu.update(); s += mpu.getGyroZ(); delay(3); }
  gyroZoffset = s / 500.0;
}

// ===== ToF sensors =====
void leeTof() {
  VL53L0X_RangingMeasurementData_t m;
  tofIzq.rangingTest(&m, false);
  lastI = (m.RangeStatus == 4) ? 999.0 : m.RangeMilliMeter / 10.0;
  tofDer.rangingTest(&m, false);
  lastD = (m.RangeStatus == 4) ? 999.0 : m.RangeMilliMeter / 10.0;
}
float leeTofEsquina() {
  VL53L0X_RangingMeasurementData_t m;
  if (esquinaIzq) tofIzq.rangingTest(&m, false);
  else            tofDer.rangingTest(&m, false);
  return (m.RangeStatus == 4) ? 999.0 : m.RangeMilliMeter / 10.0;
}
float leeTofExterno() {
  VL53L0X_RangingMeasurementData_t m;
  if (esquinaIzq) tofDer.rangingTest(&m, false);
  else            tofIzq.rangingTest(&m, false);
  return (m.RangeStatus == 4) ? 999.0 : m.RangeMilliMeter / 10.0;
}

// Reads the front ULTRASONIC sensor (cm). No echo -> 999 (far away).
float leeUltrasonico() {
  digitalWrite(TRIG_US, LOW);  delayMicroseconds(2);
  digitalWrite(TRIG_US, HIGH); delayMicroseconds(10);
  digitalWrite(TRIG_US, LOW);
  long dur = pulseIn(ECHO_US, HIGH, 25000);
  if (dur == 0) return 999.0;
  return dur / 58.0;
}

float distanciaCm()     { return encCount  / PULSOS_POR_CM; }
float distDesdeCorner() { return encCorner / PULSOS_POR_CM; }
void  resetDist()   { noInterrupts(); encCount  = 0; interrupts(); }
void  resetCorner() { noInterrupts(); encCorner = 0; interrupts(); }

// ===== Motor =====
void motor(int v) {
  digitalWrite(STBY, HIGH);
  if (v >= 0) { digitalWrite(AIN1, HIGH); digitalWrite(AIN2, LOW); }
  else        { digitalWrite(AIN1, LOW);  digitalWrite(AIN2, HIGH); v = -v; }
  analogWrite(PWMA, constrain(v, 0, 255));
}
void freno() { analogWrite(PWMA, 0); digitalWrite(STBY, LOW); }
```

</details>

<a id="bitacora-decisiones"></a>
 
## 4. Systems Thinking and Engineering Decisions
 
> [!NOTE]
> This section documents, in chronological order, the **reasoning behind every major change** in the robot: the problem or constraint that motivated it, the alternatives considered, the decision taken and the evidence that supports it. The goal is to show the complete engineering process, not just the final result.
 
Every entry follows the same format: **Context/Constraint → Options considered → Decision and rationale → Evidence/Result**.
 
### Quick summary
 
| # | Date | Decision | Category | Status |
|---|---|---|---|---|
| 1 | Start of season (no date) | RPi + Pure Pursuit for obstacle avoidance | Software | ![Replaced](https://img.shields.io/badge/-Replaced-lightgrey) (see 10) |
| 2 | May 21, 2026 | Raspberry Pi → HuskyLens + Arduino Mega | Software/Hardware | ![Current](https://img.shields.io/badge/-Current-brightgreen) |
| 3 | no date | Wheels 62.4×20 mm → 57×14 mm | Mechanical | ![Current](https://img.shields.io/badge/-Current-brightgreen) |
| 4 | no date | Batteries 6×3.7 V → 2×7.8 V/2200 mAh | Power | ![Current](https://img.shields.io/badge/-Current-brightgreen) (comparing a 7.4 V/3000 mAh alternative) |
| 5 | Before the regional | VL53L0X tested, not used at the regional | Sensors | ![Superseded](https://img.shields.io/badge/-Superseded-lightgrey) (see 8) |
| 6 | Jun 25, 2026 | Electrical failure (regulator + servo) | Risk/Maintenance | ![Resolved](https://img.shields.io/badge/-Resolved-blue) |
| 7 | Jun 28, 2026 | IR corner sensor + Open Round validation | Sensors | ![Current](https://img.shields.io/badge/-Current-brightgreen) |
| 8 | Jul 5, 2026 | Lateral sensors: ultrasonic → VL53L0X | Sensors | ![Current](https://img.shields.io/badge/-Current-brightgreen) |
| 9 | Jul 5, 2026 | Curves: gyroscope → wall following | Software | ![In testing](https://img.shields.io/badge/-In%20testing-yellow) |
| 10 | Post-regional (exact date n/a) | Avoidance: Pure Pursuit → reactive by distance | Software | ![Current](https://img.shields.io/badge/-Current-brightgreen) |
| 11 | Jul 7, 2026 | Obstacle avoidance validation | Testing | ![Partial](https://img.shields.io/badge/-Partial-orange) (straight stretch only) |
| 12 | Jul 26 – Aug 9, 2026 | Mounting failure in the lateral sensor mounts → redesign and printing of the definitive mount | Mechanical | ![Resolved](https://img.shields.io/badge/-Resolved-blue) |
| 13 | Aug 3, 2026 | Drift correction: undersized axle hole in the printed motor mount | Mechanical | ![Improved](https://img.shields.io/badge/-Improved-yellowgreen) |
| 14 | Aug 9, 2026 | Recalibration of avoidance thresholds + validation over ¾ of a lap | Software/Testing | ![Current](https://img.shields.io/badge/-Current-brightgreen) |
| 15 | Aug 10, 2026 | Discontinuation of the infrared corner sensor | Sensors | ![Retired](https://img.shields.io/badge/-Retired-lightgrey) |
| 16 | Aug 23, 2026 | Complete Obstacle Challenge run (2 min 18 s) | Testing | ![Validated](https://img.shields.io/badge/-Validated-brightgreen) |
 
### Decision 1 — Initial obstacle avoidance architecture: Raspberry Pi + Pure Pursuit
 
- **Context/Constraint:** the obstacle avoidance system initially relied on preprogrammed maneuvers and specific cases, which limited the robot's adaptability to different obstacle positions.
- **Decision:** implement a path tracking system based on **Pure Pursuit**, using the Raspberry Pi Rev 1.3 camera as the main perception sensor. The algorithm detected the obstacle's position, generated trajectory points around it and continuously selected a look-ahead point along the trajectory to compute the required steering angle.
- **Result:** the approach produced smoother movements than fixed maneuvers, but it increased the system's complexity and processing load (see Decision 2 and Decision 10).
### Decision 2 — Vision platform change: Raspberry Pi → HuskyLens (May 21, 2026)
 
- **Context/Constraint:** the Raspberry Pi + Raspberry Pi camera gave functional results, but increased the overall complexity of the system and required a higher processing load to run the detection algorithms.
- **Options considered:** optimize the vision pipeline on the Raspberry Pi vs. migrate to a dedicated vision sensor with preconfigured recognition modes.
- **Decision and rationale:** the Raspberry Pi and its camera were replaced with a **HuskyLens camera**, leaving the **Arduino Mega** as the sole controller. The HuskyLens simplifies the development of the detection system (built-in computer vision functions) and frees the Arduino Mega for control and navigation tasks, improving responsiveness during dynamic maneuvers.
- **Evidence/Result:** HuskyLens integrated since May 21, 2026; we continue calibrating and evaluating the camera's different recognition modes.
### Decision 3 — Wheel and footprint redesign
 
- **Context/Constraint:** the previous wheels (62.4 × 20 mm) offered good stability but increased the robot's overall size.
- **Options considered:** keep the current wheels vs. adopt a smaller model (Lego Spike Prime, 57 × 14 mm).
- **Decision and rationale:** the wheels were replaced with the 57 × 14 mm model. These wheels have a higher wear coefficient, which improves durability and grip on competition surfaces; their smaller size also reduces dimensions and weight.
- **Evidence/Result:** height reduced from 23.9 cm to 20.3 cm; width from 15 cm to 14.6 cm. The resulting structure is more compact and lighter, with a slightly improved center of gravity that favors stability in curves and fast maneuvers.
### Decision 4 — Simplification of the power system
 
- **Context/Constraint:** the previous arrangement of 6 batteries of 3.7 V (~12 V, 2000 mAh) met the energy requirements, but took up a lot of internal space and increased the robot's weight.
- **Options considered:** keep the 6-cell arrangement vs. consolidate into fewer cells of higher voltage.
- **Decision and rationale:** **2 batteries of 7.8 V, 2200 mAh** were adopted, freeing internal space, reducing weight and simplifying energy management.
- **Evidence/Result:** space and weight reduction confirmed, with enough autonomy to complete the rounds. **Iteration in progress:** in parallel we are evaluating a second battery set (7.4 V, 3000 mAh), lighter and more compact but of lower voltage, comparing it against the current one in autonomy, electrical stability under motor load, weight and on-track performance. The 7.8 V / 2200 mAh arrangement remains the current configuration as long as the comparison does not show a clear advantage for the other one.
### Decision 5 — Experimental test of the VL53L0X sensor (not deployed at the regional)
 
- **Context/Constraint:** the VL53L0X laser sensor (Time-of-Flight) was integrated experimentally into the electronic architecture to improve front obstacle detection and short-range accuracy.
- **Risk mitigation decision:** due to time constraints during integration and calibration, the team decided **not to use the sensor during the regional competition**, prioritizing system reliability over incorporating an insufficiently tested component.
- **Result:** the sensor was documented as an important improvement for future iterations and was permanently integrated after the regional (see Decision 8).
### Decision 6 — Electrical incident and failure response (June 25, 2026)
 
- **Context:** during testing, the voltage regulator and the steering servo (MG90S) suffered a short circuit and were damaged.
- **Impact:** navigation tests were temporarily halted while the origin of the failure was diagnosed.
- **Mitigation action:** replacement of both components and review of the wiring and associated connections, with the goal of preventing the failure from recurring.
- **Status:** Resolved with the corresponding replacement of components.
### Decision 7 — Rear corner sensor and validation with open round tests (June 28, 2026)
 
- **Context/Constraint:** the robot did not have a sensor dedicated to detecting the track's corner lines, which could produce inaccuracies when starting or ending a turning maneuver.
- **Decision and rationale:** an **MH Sensor Series** infrared sensor (based on the TCRT5000 with an LM393 comparator) was added at the lower rear end, complementing the information from the lateral VL53L0X sensors and the wall following algorithm.
- **Evidence/Result:** in the open round tests that same day, the robot completed **3 laps consistently**, parked correctly in the starting quadrant and finished the run in **75 seconds**, jointly validating the lateral VL53L0X sensors, the wall following and the new corner sensor. See the video in the [Open Challenge](#open-challenge) section.
### Decision 8 — Lateral sensors: from ultrasonic to VL53L0X ToF (July 5, 2026)
 
- **Context/Constraint:** the ultrasonic sensors (HC-SR04P) used on the sides were susceptible to reading variations depending on the angle or the material of the wall.
- **Options considered:** keep the ultrasonic sensors vs. permanently deploy the VL53L0X, already validated experimentally (Decision 5) but not used at the regional due to time limits.
- **Decision and rationale:** the lateral ultrasonic sensors were replaced with **VL53L0X ToF** sensors which, being based on infrared light, offer more stable and consistent measurements at short range than the reflection of sound waves.
- **Result:** the VL53L0X becomes a permanent part of the electronic architecture, specifically on the sides.
### Decision 9 — Navigation in curves: from gyroscope to wall following

- **Context/Constraint:** the robot solved the whole Open Challenge relying on a single sensor: the gyroscope (GY-9250). It drove straight holding a heading and turned the corners against that same heading. The underlying problem is that the gyroscope estimates the angle by **integrating** the angular rate, so its error accumulates over time and grows with speed: the faster the robot went, the faster its heading estimate degraded and the more it deviated from the trajectory. That forced us to **lower the robot's speed** so the gyroscope could keep a reliable heading — we were paying speed to buy accuracy.
- **Options considered:** keep lowering the speed and fine-tuning the gyroscope calibration, or switch to an **absolute** reference instead of a cumulative one: directly measuring the distance to the outer wall with the VL53L0X sensors (Decision 8).
- **Decision and rationale:** we migrated to a **wall following** scheme, where the system constantly measures the distance to the outer wall and corrects the steering to keep it constant. Unlike gyroscope heading, this measurement **does not accumulate error**: each reading is independent and refers to something physical and fixed (the wall), so a bad data point does not contaminate the following ones. That eliminated the reason we had to go slowly.
- **Evidence/Result:** with the previous scheme, relying solely on the gyroscope, the best Open Challenge time was **95 seconds**. After migrating to wall following we were able to **raise the robot's speed** without losing trajectory accuracy, bringing the run down to **75 seconds** — an improvement of **≈21 %** directly attributable to the change of navigation reference.
- **Systems thinking note:** this algorithm change was possible *thanks to* the previous hardware decision (Decision 8) — an example of how a sensing decision directly enabled a performance improvement in the navigation software. The gyroscope was not removed from the robot: it is still the heading reference in the Obstacle Challenge, where the pillars interrupt the continuity of the wall and wall following is not viable as the sole reference.
### Decision 10 — Obstacle avoidance: from Pure Pursuit to reactive distance-based tracking
 
- **Context/Constraint:** Pure Pursuit required generating trajectory points and constantly recomputing a geometric path around each obstacle, which added computational and design complexity.
- **Options considered:** keep and refine Pure Pursuit vs. adopt a simpler reactive scheme based on distance thresholds.
- **Decision and rationale:** Pure Pursuit was removed for obstacle avoidance and a reactive scheme was adopted:
  1. The robot drives in a straight line.
  2. On detecting an obstacle at 50 cm, it starts tracking it, keeping it centered in the HuskyLens field of view.
  3. At 30 cm, it starts turning to avoid it.
  4. The turn continues until the obstacle is out of sight.
  5. A 10-frame re-centering protocol with respect to the lane is executed before continuing straight.
  **This change affects only the obstacle avoidance system**; the sensors and the wall following algorithm (Decision 9) were not modified.
- **Evidence/Result:** validated in the obstacle avoidance tests of July 7, 2026 (see Decision 11).
### Decision 11 — Validation: obstacle avoidance tests (July 7, 2026)
 
- **Context:** validate on the track the reactive scheme from Decision 10.
- **Evidence/Result:** on a straight section, the robot correctly avoided a red pillar (kept to its right) and a green pillar (kept to its left). [See the video of this test](https://youtu.be/mim8iLk7CLE) (historical video; the current video in the [Obstacle Challenge](#obstacle-challenge) section corresponds to a more recent and complete run).
- **Current status:** this test covers only a straight stretch of the track. Testing continues in order to validate pillar avoidance in different positions and color combinations along the complete circuit.
### Decision 12 — Mounting failure in the lateral sensor mounts: iterating on the solution (July 26 – August 9, 2026)

- **Context/Constraint:** it was found that the MDF mounts of the lateral laser sensors (VL53L0X), when fitted into the chassis base, ended up slightly crooked with a small downward tilt. This caused the sensor not to read the distance to the wall correctly, but instead to detect the distance to the floor.
- **Options considered:** manually adjust/shim the existing MDF mounts vs. design a custom mount in CAD and 3D print it in PLA.
- **Initial plan (July 28, 2026):** the team concluded that the best solution was to design new mounts digitally and 3D print them in PLA, with a corrected mounting angle, to replace the current MDF parts.
- **Iteration 1 — the solution actually implemented:** in practice, the team decided **not to fabricate new mounts with a different angle**. Instead, the same existing MDF mounts were reused, relocated and glued to the underside of the chassis's second deck, just above their original position. This raised the sensors to approximately **8 cm** above the floor.
- **New finding (August 9, 2026):** since the track walls are **10 cm** tall, this new position (8 cm) leaves little margin with respect to the top edge of the wall, creating uncertainty about how reliable the distance reading is at that height.
- **Iteration 2 — definitive solution (from August 9, 2026):** the team will design mounts that keep the same angle and symmetry as the current ones, but with **greater length**, and will return them to the **original mounting position** (not the temporary location under the second deck). This way, the sensors will end up higher than in their original position, without depending on the provisional relocation.
- **Iteration 3 — final 3D printed part:** a new mount was designed and printed (`SoporteLaser.STL`, see [Mechanical Design Parts](#piezas-cad)) that raises the sensor almost 4 cm above the original MDF mount, correcting the tilt. The team confirms that this solved the false readings and produced more consistent behavior.
- **Current status:** ✅ **Resolved.**
### Decision 13 — Drift correction: undersized axle hole in the printed motor mount (August 3, 2026)

- **Context/Constraint:** when measuring the robot's performance on straight stretches (~3 meters), an excessive **drift** (angular deviation) was detected: instead of moving in a straight line, the robot opened up, forming a triangle-shaped trajectory with respect to the ideal line. The measured deviation was approximately **45°**.
- **Diagnosis:** when testing the robot suspended in the air (with no contact with the floor), it was observed that one of the wheels (left side, seen from behind the robot) did not turn. The cause: the axle hole in the 3D printed part that holds the motor (orange mount, visible in the rear photo of the robot) was slightly smaller than the actual size of the Lego axle, creating an excessive press fit. This meant that wheel only turned when there was contact and friction with the floor, advancing more slowly than the opposite side and generating the drift.

<div align="center">
<img src="v-photos/rearView.jpeg" width="220" alt="Rear view of the robot, printed motor mount">
<br><sub>Rear view — printed motor mount (orange part) where the undersized axle hole was found.</sub>
</div>

- **Decision and corrective action:** the hole in the printed mount was enlarged with a drill bit, allowing the axle to turn freely under any circumstance, both suspended in the air and in contact with the track.
- **Evidence/Result:** the wheel now turns correctly in both conditions, both suspended in the air and on the track. The robot's drift is **much smaller** than before the correction, and the Open Challenge run time improved noticeably in that same session — a result consistent with a straighter trajectory requiring fewer corrections along the run.
- **Status:** improvement confirmed; the team continues to monitor the remaining drift in order to reduce it further.
### Decision 14 — Recalibration of avoidance thresholds and validation over ¾ of a lap (August 9, 2026)

- **Context:** the reactive obstacle avoidance scheme (Decision 10) had only been validated on a straight stretch of the track (Decision 11, July 7, 2026), using fixed thresholds of 50 cm (start of tracking) and 30 cm (start of the avoidance turn).
- **Decision and change:** after iterative testing, the distance thresholds were recalibrated:
  - **Obstacle tracking:** now between **60 cm and 30 cm** (previously: a single 50 cm threshold).
  - **Start of the avoidance sequence:** now between **25 cm and 20 cm** (previously: a single 30 cm threshold), with a **progressive deviation** as the robot approaches the obstacle, instead of a more abrupt turn from a single threshold.
  - **Color rule (unchanged):** green pillar → avoid on the left; red pillar → avoid on the right.
- **Evidence/Result:** the recalibrated scheme was validated in a test covering **≈3/4 of a complete lap** of the track — much broader coverage than the previous test, which was limited to a straight stretch (Decision 11) — detecting and avoiding obstacles by color and distance consistently. [See the video of this test](https://youtube.com/shorts/EIXM7CX9vMc?feature=share) (historical video; the current video in the [Obstacle Challenge](#obstacle-challenge) section corresponds to a more recent and complete run).
- **Status:** current thresholds of the obstacle avoidance system.
### Decision 15 — Discontinuation of the infrared corner sensor (August 10, 2026)

- **Context:** the MH Sensor Series infrared sensor, added on June 28, 2026 to read the corner lines and complement the lateral VL53L0X sensors (Decision 7), was in use until August 9, 2026.
- **Decision:** it was discontinued as of **August 10, 2026**. It was removed from the BOM and from the vehicle's current specifications ("Power and Sensors" table).
- **Note:** the Decision Log entries and the Open Challenge video documenting tests prior to this date (Decision 7, June 28, 2026) are kept unchanged, since they correctly describe the state of the robot at that time.
### Decision 16 — Validation: complete Obstacle Challenge run (August 23, 2026)

- **Context:** up to this date, the Obstacle Challenge had only been validated in segments: a straight section (Decision 11) and ≈3/4 of a lap (Decision 14). What remained was to prove that the state machine could sustain its behavior over a long run, where corners, dodges and gyroscope drift accumulate.
- **Evidence/Result:** on **August 23, 2026** the robot completed a **2 min 18 s** run, autonomously chaining the complete cycle: straight heading control, detection and avoidance of pillars by color, and corner detection and turning — without manual intervention and without displacing any sign. [See the video](https://youtu.be/mVZCY8PyXOI).
- **What this test validates:** that the three pieces of the system (color perception, gyroscope heading control and ToF corner detection in long range mode) coexist without interfering with each other over a long run, and that the safety timeouts described in point 10 of [Software Architecture](#ingenieria-defensiva) keep the robot out of blocked states.
- **Status:** current behavior of the Obstacle Challenge.
[⬆ Back to top](#indicleto)
 
---

<a id="reproducibilidad"></a>

## 5. Reproducibility and Repository Structure

> [!NOTE]
> This section exists so that **another team could take this repository and rebuild the robot** with only what is here: what each file is, how it is compiled, and how the change history is managed.

### Repository structure

```
├── README.md                          # This document
├── abierto.ino                        # Open Challenge code (see Software Architecture, 3.1)
├── obstaculos.ino                     # Obstacle Challenge code (see Software Architecture, 3.2)
├── cad/                                # Mechanical design parts in .STL (see Mobility and Mechanical Design)
│   ├── S25_chasis_rev18.STL
│   ├── S25_Plataforma_Soporte_Rev_8.STL
│   ├── S25_Soporte_de_motor_y_transmision_Rev_4B.STL
│   ├── S25_Mangueta_Rev_2.STL
│   ├── R26_EnlaceDireccion_Rev7.STL
│   └── SoporteLaser.STL
├── schemes/                            # Component photos (BOM) and wiring diagrams
├── v-photos/                            # Vehicle photos (6 views + test photos)
└── t-photos/                            # Team photo
```

### How to compile and upload each program

1. Install the **Arduino IDE**.
2. Install the libraries each program uses (Library Manager → search by name):
   - **Both programs:** `Wire` (included with the IDE), `MPU6050_light`.
   - **`abierto.ino`** (Open Challenge): `Adafruit_VL53L0X`, `Servo` (included with the IDE).
   - **`obstaculos.ino`** (Obstacle Challenge): `Adafruit_VL53L0X`, `SCServo` (Waveshare library for the ST3215), `HUSKYLENS` (official DFRobot library).
3. Open the file corresponding to the challenge (`abierto.ino` or `obstaculos.ino`).
4. Select the **Arduino Mega 2560** board and the corresponding port.
5. Verify that the HuskyLens is set to **I2C** mode (applies only to `obstaculos.ino`) and that the color recognition algorithm has learned the colors red and green before running the program.
6. Upload the program. In `obstaculos.ino`, the robot waits for the start button (pin 23) to be pressed before it starts moving.

### Commit and versioning conventions

- **Descriptive commit messages**, along the lines of: `feat: add recalibration of dodge thresholds`, `fix: correct axle hole in motor mount`, `docs: update README with power architecture`.
- **Commit history compliant with the rules**: at least 3 commits, the first with at least 1/5 of the final code and at least two months before the competition, the second at least one month before, and the third (the one that is evaluated) at least two weeks before.
- **Version tags** at the important milestones of the Decision Log, for example: `v1.0` (Mexicali Regional), `v1.1` (post-regional: lateral VL53L0X + wall following), `v1.2` (reactive avoidance algorithm), `v1.3` (ST3215-HS servo + redesigned mounts).

### Traceability between code, tests and documentation

Every substantive change documented in the [Decision Log](#bitacora-decisiones) has its corresponding evidence elsewhere in the repository, so it can be verified rather than just taken on written word:

| Type of change | Where the code/evidence lives |
|---|---|
| Hardware changes (sensors, chassis, servos) | [Mechanical Design Parts](#piezas-cad) (`/cad`) and [BOM](#bom) |
| Algorithm changes | `abierto.ino` / `obstaculos.ino`, explained in [Software Architecture](#arquitectura-software) |
| On-track validation | [Competition Videos](#competition-videos) |

[⬆ Back to top](#indicleto)
 
---
 
<a id="competition-videos"></a>

## Competition Videos


 
In accordance with the official WRO 2026 – Future Engineers rules, each team must publish a video on YouTube (public or accessible via link) documenting the autonomous driving of the vehicle for each challenge, with a minimum duration of 30 seconds per video.
 
| Challenge | Status | Link | Run duration |
|------|--------|--------|-------------------------|
| **Open Challenge** | ✅ Published | [Watch on YouTube](https://youtu.be/jBpTh44YIUg) | 75 s |
| **Obstacle Challenge** | ✅ Published | [Watch on YouTube](https://youtu.be/mVZCY8PyXOI) | 2 min 18 s |
 
### Open Challenge
 
<div align="center">
<a href="https://youtu.be/jBpTh44YIUg"><img src="https://img.youtube.com/vi/jBpTh44YIUg/0.jpg" width="320" alt="Open Challenge video"></a>
</div>
The video corresponds to the open round test carried out on **June 28, 2026**. In the documented run, the robot:
 
- Autonomously performs **3 consecutive laps** around the track.
- Parks in the **starting quadrant** of the course, without manual intervention.
- Completes the entire challenge in **75 seconds**.
**Systems involved during the run:** wall following using lateral VL53L0X sensors for taking the curves, MH Sensor Series infrared sensor at the lower rear end for reading corner lines, and HuskyLens + Arduino Mega as the main control unit.
 
### Obstacle Challenge
 
<div align="center">
<a href="https://youtu.be/mVZCY8PyXOI"><img src="https://img.youtube.com/vi/mVZCY8PyXOI/0.jpg" width="320" alt="Obstacle Challenge video"></a>
</div>
The video corresponds to a complete obstacle avoidance run carried out on **August 23, 2026**, lasting **2 min 18 s** (see Decision Log, **Decision 16**). The robot detects and avoids the obstacles according to their color and distance:
 
- **Green pillar:** avoided on the **left**.
- **Red pillar:** avoided on the **right**.

**Systems involved during the run:** color detection with the HuskyLens camera; obstacle tracking keeping it centered in the camera between **60 and 30 cm** away; start of the avoidance sequence between **25 and 20 cm**, with progressive deviation as the robot approaches; corner detection and turning; and a 10-frame re-centering protocol when the obstacle goes out of sight.

> [!NOTE]
> The earlier Obstacle Challenge videos (straight stretch from July 7, ≈3/4 of a lap from August 9) are kept in the Decision Log as historical evidence of the system's evolution — see Decisions 11 and 14.
 
[⬆ Back to top](#indicleto)
 
---
 
<a id="bom"></a>
 
## BOM (Bill of Materials)
 
| Component | Power requirement | Image | Price |
|------------|--------------------------|--------|--------|
| Arduino Mega 2560 | 0.25-0.5W | <img src="schemes/ArduinoMega.jpg" width="80"> | ≈ 24.46 USD |
| DC Motor with Encoder: GA37-520 300RPM | 5.55-16.65W | <img src="schemes/MOTORDC.jpg" width="80"> | ≈ 22.12 USD |
| TB6612FNG H-Bridge | 0.025W | <img src="schemes/HBRIDGE.jpg" width="80"> | ≈ 4.35 USD |
| Servo Motor: MG90S (Open Challenge) | 0.5-2.5W | <img src="schemes/SERVO.webp" width="80"> | ≈ 4.08 USD |
| Serial bus servo: Waveshare ST3215-HS (Obstacle Challenge) | 0.75-6.75W (0.1-0.9A at 7.5V) | <img src="schemes/ST3215.jpg" width="80"> | ≈ 18.00 USD |
| Ultrasonic Sensor: HC-SR04P x1 (front) | 0.075W | <img src="schemes/ULTRASONICO.webp" width="80"> | ≈ 0.90 USD |
| ToF Laser Sensor: VL53L0X x2 (lateral) | ≈ 0.10W | <img src="schemes/laser.jpg" width="80"> | ≈ 9.00 USD |
| Accelerometer/Gyroscope: GY-9250 | 0.033W | <img src="schemes/GIRO.jpg" width="80"> | ≈ 9.24 USD |
| LED x4 | 0.264W | <img src="schemes/LED.png" width="80"> | ≈ 0.44 USD |
| Buzzer | N/A | <img src="schemes/BUZ.jpg" width="80"> | ≈ 0.27 USD |
| SEN0336 HuskyLens PRO OV5640 | 3.3~5.0V | <img src="schemes/HUSKY.webp" width="80"> | ≈ 40.65 USD |
| **Total** | | | **≈ 133.51 USD** |

> [!NOTE]
> The infrared sensor (MH Sensor Series) was removed from this BOM because it was discontinued as of **August 10, 2026** — see Decision Log, **Decision 15**.

> [!NOTE]
> The robot uses a different servo in each challenge: **MG90S** in the Open Challenge and **ST3215-HS** in the Obstacle Challenge — see [Software Architecture](#arquitectura-software) and [Power Architecture](#arquitectura-potencia).
 
[⬆ Back to top](#indicleto)
