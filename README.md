# Team Bunker Sierra – WRO Future Engineers 2025

We are a team from Baja California, México, composed of two members: Diego Alejandro Salas Díaz and Jacobo Arteaga Castañeda from Bunker Robotics. Both team members have experience in other competitions, specifically the Robomission category, totaling 8 years of combined experience.

This is our first year in the Future Engineers category. We began our preparation at the start of the year (January 2025), and over time, our robot has gone through several iterations. In fact, our current robot looks significantly different from the version used in the regional stage.

The following photo shows the 3 team members (from left to right):

Jacobo Arteaga Castañeda - Diego Alejandro Salas Díaz - Andrea Perez Bueno (Coach)

![IMG-Serius](/t-photos/IMG-Serius.jpeg)

In this document, we will detail our robot's components, their functions, and how they work together. We will also explain some of the key changes we have made to the robot, supported by visual evidence.

Please note: All requested supplementary information will be available in its corresponding folder within the repository.

## Robot Specifications:

**Vehicle photos rev.14 (Mexico National Final):**
| View | Photo |
|-----------|--------------------|
| Top View | ![IMG-Robot_TopView](/v-photos/IMG-Robot_TopView.jpeg) |
| Bottom View | ![IMG-Robot_BottomView](/v-photos/IMG-Robot_BottomView.jpeg) |
| Front View | ![IMG-Robot_FrontView](/v-photos/IMG-Robot_FrontView.jpeg) |
| Back View | ![IMG-Robot_BackView](/v-photos/IMG-Robot_BackView.jpeg) |
| Right View | ![IMG-Robot_RigthSideView](/v-photos/IMG-Robot_RigthSideView.jpeg) |
| Left View | ![IMG-Robot_LeftSideView.](/v-photos/IMG-Robot_LeftSideView.jpeg) |

**Vehicle photos rev.15 (New rev. for OC Panama):**
| View | Photo |
|-----------|--------------------|
| Top View | ![IMG_TV](/v-photos/OC_Panama/IMG_TV.JPG) |
| Bottom View | ![IMG_BMV](/v-photos/OC_Panama/IMG_BMV.JPG) |
| Front View | ![IMG_FV](/v-photos/OC_Panama/IMG-FV.jpeg) |
| Back View | ![IMG_BKV](/v-photos/OC_Panama/IMG-BKV.jpeg) |
| Right View | ![IMG_RV](/v-photos/OC_Panama/IMG-RV.jpeg) |
| Left View | ![IMG_LV](/v-photos/OC_Panama/IMG-LV.jpeg) |

- **Weight (Maximum Load):** 1166 grams.
- **Width:** 1.5cm | **Length** 19.5cm | **Heigth:** 23.9cm

**Robot Bill of Materials (BOM)**

| Component | Power Requirements | Image | Price |
|-----------|--------------------|-------|-------|
| Arduino Mega 2560 | 0.25-0.5W | ![IMG-AM](/schemes/IMG-AM.jpg) | ≈ 24.46 Dlls |
| Raspberry Pi 4 Model B (1GB RAM) | 3.06-6.12W | ![IMG-RBPi4](/schemes/IMG-RBPi4.jfif) | ≈ 46.90 Dlls |
| Raspberry Pi Camera Rev 1.3 | 0.83W | ![IMG_Camara_RB1.3](/schemes/IMG_Camara_RB1.3.webp) | ≈ 25 Dlls |
| DC Motor with Encoder: GA37-520 300RPM | 5.55-16.65W | ![IMG-Motor12v](/schemes/IMG-Motor12v.jpg) | ≈ 22.12 Dlls |
| H-Bridge TB6612FNG | 0.025W | ![IMG-TB6612FNG](/schemes/IMG-TB6612FNG.jpg) | ≈ 4.35 Dlls |
| Servo Motor: MG90S | 0.5-2.5W | ![IMG-MG90S](/schemes/IMG-MG90S.jpg) | ≈ 4.08 Dlls |
| Ultrasonic Sensors: HC-SR04P x3 | 0.225W | ![IMG-HC-SR04P](/schemes/IMG-HC-SR04P.jpg) | ≈ 2.72 Dlls **(one unit)** |
| Accelerometer/Gyroscope: GY-9250 | 0.033W | ![IMG-GY9250](/schemes/IMG-GY9250.jpg) | ≈ 9.24 Dlls |
| LEDs x4 | 0.264W | ![IMG-LED](/schemes/IMG-LED.webp) | ≈ 0.11 Dlls **(one unit)** |
| Passive Buzzer |  | ![IMG-Buzzer.jpg](/schemes/IMG-Buzzer.jpg) | ≈ 0.27 Dlls |
| **Total** | --- | --- | **≈ 145.02 Dlls** |


*Electrical Parts:*

- Arduino Mega 2560

- Raspberry Pi 4 Model B (1GB RAM)

- Raspberry Pi Camera Rev 1.3

- Motor Driver: IMS-2 WINGXINE

  - **Update 20/08/2025:** H-Bridge TB6612FNG

- Voltage Regulator: LM2596 Step-Down Module

- DC Motor with Encoder: GA37-520 300RPM

- Servo Motor: MG90S

- 3x Ultrasonic Sensors: HC-SR04P

- Accelerometer/Gyroscope: MPU-6050

  - **Update 12/09/2025:** Accelerometer/Gyroscope: GY-9250

- 4x LEDs

- Batteries: 6x 3.7v 18650 9900mAh

  - **Update 14/09/2025:** 8x 3.7v 18650 9900mAh

  - **Update 25/09/2025:** 3x 3.7v 18650 3000mAh
    
  - **Update 02/10/2025:** 6x 3.7v 18650 3000mAh

- Battery Holders: 3x 18650 Holders

  - **Update 14/09/2025:** 4x 18650 Holders

  - **Update 25/09/2025:** 2x 18650 Holders
    
  - **Update 02/10/2025:** 4x 18650 Holders

- 1x Passive Buzzer

- 2x Mini Protoboards

*Mechanical Parts:*

- MDF Chassis x3

- MDF Linkage Steering System

- 4x Lego Wheels (62.4mm x 20mm)

- 3D-Printed Motor Mount

- Raspberry Pi Camera Rev 1.3 Mounts

- 3x Ultrasonic Sensor Mounts

- Lego Differential Gear

- 3D-Printed Spur Gear (24-tooth)

- Lego Technic Parts

**Update 25/09/2025**

***Update for the Panama Open Championship***

We have implemented several changes to the robot's design, specifically focusing on reducing its footprint. In other words, we have reduced the robot's width and length while taking full advantage of the established height limit (30 cm). We have redistributed the components accordingly to optimize the use of space.

Here is the list of the STL desings of all the parts of the robot chassis that where changed:

- [S25 Chassis Rev15B](/models/Open_Championship_Panama/S25_Chasis_Rev15B.STL).
- [BS Batteries Rev3.STL](/models/Open_Championship_Panama/BS_Baterias_Rev3.STL).
- [C3 Batteries Rev3.STL](/models/Open_Championship_Panama/C3_Baterias_Rev3.STL).
- [S25 Level 3 Rev3.STL](/models/Open_Championship_Panama/S25_3er_Piso_Rev3.STL).


We have also significantly reduced the robot's turning circumference. Previously, when the robot executed a full turn, the diameter of the circle it traced was 62 cm. This was quite large and allowed the robot to collide easily with the field's barriers and obstacles (in both challenges). By reducing the robot's overall size, the turning circle diameter is now 30 cm. We managed to cut it in half, giving the robot a greater margin of error to avoid collisions with various objects on the field. 

Another major change was made to the robot's power system. To be frank, our previous design was poorly planned. For the Open Championship, we opted to use **two packs in parallel of three 3.7V 30000mAh batteries connected in series(3S2P)**. 

Finally, we added two lamps to the robot to improve our camera's optics. By generating our own light source, detecting obstacles becomes a simpler task, as it no longer depends on the varying light conditions and parameters of the different environments where the robot operates. 

Here is the list of the STL desings of the camera parts that where changed:

- [Light L Mount](/models/Open_Championship_Panama/Soporte_L_Rev1.STL).
- [Camera Mount_Rev3](/models/Open_Championship_Panama/Soporte_Camara_Rev3.STL).

This is the brand and especifc model of the lamps tha wew used: **Defiant 226 129**

![IMG-Lamparas](/schemes/IMG-Lamparas.jpeg)

  
Mechanical Parts
===

*You are about to see all the information contained in our [models](/models/README.md). folder. If you wish to only view the information from this specific section, you can access it directly via the hyperlink (with the exception of the updates for the Panama Open Championship).*

One key point we would like to emphasize is that the majority of our mechanical parts were manufactured using laser cutting. Another small section of parts, primarily for the steering system, were 3D-printed.

## Vehicle Chasis
The vehicle consists of a chassis and two additional levels (floors), each housing different robot components:

- **Chassis:** Robot Motion Management
  - Steering system, DC drive motor, Motor driver, and a Mini Protoboard.
  - [Chassis View](/models/S25_Chasis_Rev12.STL).
- **Level 1:** Robot Controllers
  - Arduino Mega, Raspberry Pi y RaspBerry pi Camara rev 1.3
  - [Level 1 View](/models/S25_BS_Rev6.STL).
- **Level 2:** Electrical Components
  - Mini Protoboard with HMI and Gyro sensor.
  - [Level 2 View](/models/S25_Piso3_Rev1.STL).
 
As you may notice, the filenames for all models include their revision number. For example, the chassis is on its 12th revision. This reflects every change we have needed to make throughout the season. It is the part that has undergone the most changes.

As mentioned in other sections of the repository, our robot looked very different during the regional stage compared to its current form for the national stage. This is the main reason why this particular part has been modified so extensively. It was from **Rev8** onwards that it began to take on its current appearance.

**Update 14/09/2025**

**1. Chassis and Power System Modification**

**Motivation:** During performance testing for the obstacle challenge, we observed excessive energy consumption from the Raspberry Pi module. This was causing a rapid voltage drop in the batteries (~0.08V per complete run), which compromised the robot's operational autonomy to successfully complete multiple attempts or competition rounds.

**Implemented Solution:** To resolve this energy bottleneck, we expanded the power system by integrating a fourth battery holder, connected in parallel with the existing ones. This configuration:

**Increases the total capacity (Ah)** of the battery bank.

**Maintains the nominal operating voltage** for all components.

**Provides greater autonomy** by distributing the current demand across more power sources.

**Additional Changes:** As part of the chassis reconfiguration, we re-optimized the placement of the voltage regulator and the protoboard to improve accessibility, aesthetics, and cable management.

- [S25_Chasis_Rev14](/models/S25_Chasis_Rev14.STL).

To keep the battery holders securely in place, we designed another component (there are two of these in the robot) capable of holding two battery holders simultaneously. You can view the part here:

- [SB_DrakoR_Rev2](/models/SB_DrakoR_Rev2.STL).

## Mobility Management
**Motor Mount**

![IMG_Soporte_Motor_Rev1](/schemes/IMG_Soporte_Motor_Rev1.jpeg)

*This is what the first version looked like. It allowed for basic testing, but the motor was not secured to the mount, which caused disturbances and instability.*

-[Soporte para motor Rev3](/models/S25_Soporte_Motor_y_Transmision_Rev3.STL) 

*In the current revision, the motor is firmly secured to the mount, eliminating disturbances caused by movement.* We had a similar case with the gear that connects to the motor.


**Motor Axle Support**

The output shaft of our motor couples to LEGO axles that are connected directly to the wheels. During initial testing, we noticed that these axles were bending. This created an undesired additional force on the motor's Z-axis and caused inconsistent performance in each test run.

As a solution, we designed and implemented a custom support for these axles. Once installed, the axles remain perfectly aligned at a 180° angle, eliminating bending and ensuring uniform and predictable behavior.
- [Motor Axle support](/models/S25_Soporte_Eje_Motor_Rev1.STL).

**24-Tooth Spur Gear**

Initially, we used a standard 24-tooth LEGO Spur gear, drilling out its center to fit the motor's main shaft. This allowed us to validate the behavior of the differential system.

However, this provisional solution had two critical problems: the hole was quickly deformed by wear and tear, and it generated considerable backlash. To resolve this, we designed our own gear, maintaining the standard LEGO dimensions but with the exact inner diameter for our shaft. This ensured a perfect fit and eliminated the backlash.

- **Old Gear:**

  - ![IMG_Spur24D_Lego](/models/IMG_Spur24D_Lego.jpeg)


- **Proposed Gear by the Team:**

  - [New Custom Gear](/models/S25_Spur_24D_Rev3.STL)


**Steering System** 

Our steering system operates through a **linkage mechanism** connected to the **steering knuckles**. At the center of one of these links, a custom-made component is directly attached to an **MG90S servo motor**, allowing us to position the steering mechanism precisely at the specific angles required by our navigation algorithm.

The links were manufactured from **MDF** due to its rigidity and low weight, while the knuckles and connectors were 3D-printed to achieve complex geometries and reduced weight.

![IMG_Direccion_V1](/models/IMG_Direccion_V1.jpeg)
![IMG_Direccion_V2](/models/IMG_Direccion_V2.jpeg)

- **Steering System Components:**
  - [Servo to Beam 1 Connector](/models/S25_Conexion_Servo-enlace_Rev2.STL).
  - [Beam 1](/models/S25_Enlace_Direccion_Rev5.STL).
  - [Beam 2](/models/S25_Soporte_Servo_Rev4.STL).
  - [Spacers](/models/S25_Rondana_Direccion_Rev1.STL).
  - [Steering Knuckles](/models/S25_Mangueta_Rev1.STL).

The last component (steering knuckles) was printed in two versions: one with the original design and another mirrored version.

**Wheels**

We use **62.4mm** LEGO wheels due to our positive experience with them in previous Robomission competitions. We trust their excellent **grip** and the **low rolling resistance** they generate, which is crucial for the robot's energy efficiency and traction.

![IMG_Llantas_62.4mm_Lego](/models/IMG_Llantas_62.4mm_Lego.webp)


## Obstacle Management

**Camera Mount**

The vision system is composed of several custom parts:

  - [Post Mount](/models/S25_Soporte_Poste_Camara_Rev1.STL).
  - [Post](/models/S25_Poste_Camara_Rev1.STL).
  - [Camera Mount](/models/S25_Soporte_Camara_Rev1.STL).
  - [70-Degree Joint](/models/S25_Union_70_grados_Camara_Rev1.STL).

The **height** and **tilt angle** of the camera are critical for our algorithm. The configuration must allow the robot to detect obstacles within a specific quadrant of the track.

- A **90°** (vertical) tilt did not allow for early detection.

- An initial **45°** tilt pointed the camera too far down, detecting obstacles too late.

- After a process of **trial and error**, we determined that a **70°** angle was optimal for our use case, as it provides the necessary field of view for timely and reliable detection.
  

**Update 07/09/2025**

We changed the camera mount angle to 63° and raised the mount height by 1/8 inch. Previously, with the mentioned specifications, the robot had a very low margin of error for positioning itself in front of obstacles and failed when detecting the position and color of the obstacles.

**Camera Angle and Height Optimization**

To improve reliability in detecting obstacle position and color, we adjusted the configuration:

- **Tilt Angle:** Changed from **70°** to **63°**.
  
[S25_Union63_grados_Poste-Soporte_CamaraRev2](/models/S25_Union63_grados_Poste-Soporte_CamaraRev2.STL).

- **Mount Height:** Increased by **1/8 inch**.
  
[S25_Soporte_CamaraRev2.STL](/models/S25_Soporte_CamaraRev2.STL).


The previous configuration, while functional, provided a **very low margin of error** for the robot to position itself correctly in front of obstacles. These fine adjustments significantly increased the success rate of detection and positioning.


## General Vehicle Electrical Diagram: 


*You are about to see all the information contained in our [schemes](/schemes/README.md). folder. If you wish to only view the information from this specific section, you can access it directly via the hyperlink (with the exception of the updates for the Panama Open Championship).*

![DE-S25_bb](/schemes/DE-S25_bb.jpg)

***Specifications:*** The **Arduino MEGA** board is powered directly through the serial communication port of the **Raspberry Pi 4** computer. The Raspberry Pi, in turn, is powered by our **LM2596** voltage regulator, set to **5.1V** and **3A**. We adjusted the regulator to 5.1V because when set exactly to 5V, the Raspberry Pi would often experience power supply issues.


## Electrical Diagrams for Each Component and Sensor:


***Clarification:*** The following diagrams show the signal connections for each sensor and electrical component. **Only* the **Power System** and **Motor with Motor Driver** diagrams include the power supply connections, as these are essential for replicating the robot. The other diagrams omit these connections to provide flexibility for other teams, but they can be referenced in the **General Electrical Diagram** shown above.


- **Ultrasonic Sensors**
  
![IMG-DE_SUS](/schemes/IMG-DE_SUS.jpg)


- **Accelerometer/Gyroscope**
  
![IMG-DE_Gyro](/schemes/IMG-DE_Gyro.jpg)


- **Servo Motor**
  
![IMG-DE_Servo](/schemes/IMG-DE_Servo.jpg)


- **Human Machine Interface**
  
![IMG-DE_HMI](/schemes/IMG-DE_HMI.png)


- **Motor 12v - Motor Driver**
  
![IMG-DE_Motor_MDirver](/schemes/IMG-DE_Motor_MDirver.png)


- **Power Stage and Raspberry Pi 4**
  
![IMG-DE_Potencia](/schemes/IMG-DE_Potencia.jpg)

  **Update 25/09/2025**
  ***Update for the Panama Open Championship***

  The following picture shows the new configuration of the robot's battery pack **(3S2P)** mentioned earlier in the repository:
  
  ![IMG-BatteryConfigRev3](/schemes/IMG-BatteryConfigRev3.jpg)



Mobility Management
===
## 12V DC Motor with Encoder
We selected this motor primarily for its **integrated encoder**. This sensor allows us to track the robot's position, which provides multiple benefits for our solution: calculating rotation count, measuring distance traveled, assisting in parallel parking, and, in general, all variables related to positioning.

![IMG-Motor12v](/schemes/IMG-Motor12v.jpg)

Initially, we used a motor from an RC toy that included a differential. We used that motor during the regional stage in Mexicali. We decided to change it because its speed was too inconsistent, and the lack of an encoder made it very difficult to maintain consistent movements. We understand that it's impossible for a robot to repeat a routine exactly, but that component made it even more challenging. Switching to a motor with an encoder and a LEGO differential gives us much greater control over these variables. To achieve this, we designed a mount to couple the motor to the LEGO differential, which went through 3 revisions.


## Movement 
To make the robot move in a straight line, we use a gyro sensor with a PD controller. This allows the robot to maintain its heading. As mentioned before, it's impossible to be perfect, but with this system the deviation is minimal, never exceeding 2° from the initial point (considering 0° as the origin).

![IMG_Offset](/schemes/IMG_Offset.jpeg)

To calculate the robot's deviation without the gyro sensor's PD control, we conducted 10 tests where the robot advanced 3 meters. During these tests, we realized the robot had a very large deviation, with an average of 14° deviation ± a standard deviation of 18°.

![IMG-OFFSET1](/schemes/IMG-OFFSET1.jpeg)

| Triangle  | Leg a (cm) | Leg b (cm) | Hypotenuse c (cm) | Angle α (°) | Angle β (°) | Right Angle (°) |
|-----------|------------|------------|-------------------|-------------|-------------|-----------------|
| T1        | 288.00     | 41.50      | 291.00            | 8.21        | 81.79       | 90.00           |
| T2        | 289.00     | 47.00      | 292.80            | 9.24        | 80.76       | 90.00           |
| T3        | 286.00     | 58.00      | 291.82            | 11.46       | 78.54       | 90.00           |
| T4        | 263.00     | 112.00     | 286.00            | 22.94       | 67.06       | 90.00           |
| T5        | 251.00     | 132.00     | 283.10            | 27.74       | 62.26       | 90.00           |
| T6        | 267.00     | 92.00      | 282.37            | 19.03       | 70.97       | 90.00           |
| T7        | 279.00     | 55.00      | 284.37            | 11.14       | 78.86       | 90.00           |
| T8        | 279.00     | 63.00      | 286.02            | 12.73       | 77.27       | 90.00           |
| T9        | 279.00     | 65.00      | 286.47            | 13.12       | 76.88       | 90.00           |
| T10       | 278.00     | 74.00      | 287.68            | 14.91       | 75.09       | 90.00           |
| Average   | 275.90     | 73.95      | 287.16            | 14.15       | 75.85       | 90.00           |

Therefore, we had to modify the dimensions of our steering knuckles, as they had a fairly high level of backlash, which was causing those results in the robot. Once the knuckle measurements were changed, we achieved a deviation of 4.88° ± a standard deviation of 2.61 degrees.

You can see here the STL archive of the Steering Kuckels whit the new adjustments: [Steering Knuckles Rev2](/models/Open_Championship_Panama/S25_Mangueta_Rev2.STL)

![IMG-OFFSET2](/schemes/IMG-OFFSET2.jpeg)

| Triangle  | Leg a (cm) | Leg b (cm) | Hypotenuse c (cm) | Angle α (°) | Angle β (°) | Right Angle (°) |
|-----------|------------|------------|-------------------|-------------|-------------|-----------------|
| T1        | 279.00     | 26.00      | 280.21            | 5.32        | 84.68       | 90.00           |
| T2        | 289.00     | 36.00      | 291.23            | 7.10        | 82.90       | 90.00           |
| T3        | 276.00     | 48.00      | 280.14            | 9.86        | 80.14       | 90.00           |
| T4        | 291.00     | 19.00      | 291.62            | 3.74        | 86.26       | 90.00           |
| T5        | 290.00     | 15.00      | 290.39            | 2.96        | 87.04       | 90.00           |
| T6        | 290.00     | 22.00      | 290.83            | 4.34        | 85.66       | 90.00           |
| T7        | 286.50     | 35.50      | 288.69            | 7.06        | 82.94       | 90.00           |
| T8        | 288.00     | 11.50      | 288.23            | 2.29        | 87.71       | 90.00           |
| T9        | 288.00     | 5.00       | 288.04            | 1.00        | 89.00       | 90.00           |
| T10       | 291.00     | 26.00      | 292.16            | 5.11        | 84.89       | 90.00           |

These changes were carried out because no matter how good our gyro-based PD control was, there was an underlying mechanical problem. It is well known that hardware issues cannot be solved with software.

Similarly, we use the gyro sensor for the robot's turns. Upon reaching the desired point, the robot turns the wheels and moves forward until it detects the target angle. Once reached, it stops the motors and returns the steering to the neutral position.

![IMG_GiroSensor](/schemes/IMG_GiroSensor.jpg)


**Update 22/12/2025: Forward and turn tests**

After the Panama International, we continued testing to get closer to 0 degrees of drift without using the gyro sensor, and we also measured the overshoot of the distance the robot travels.
These tests were performed with the following characteristics: 100 cm of forward movement, without the use of a direction sensor, without the use of ultrasonic sensors, without moving the direction during the tests and maintaining a PWM speed of 180 for the first table, 150 for the second and 165 for the third in the DC motor.
The values ​​are in centimeters for overshoot, and in degrees for drift.

     27/10/2025 Forward Tests (The steering servomotor is held at 61 degrees):

| Average Overshoot  | Overshoot Dispersion | Drift Average | Drift Dispersion |
|--------------------|----------------------|---------------|------------------|
| -6.68              | 8.4                  | 13            | 1.37             |


     07/11/2025 Forward Tests (The steering servomotor is held at 65 degrees):

| Average Overshoot  | Overshoot Dispersion | Drift Average | Drift Dispersion |
|--------------------|----------------------|---------------|------------------|
| 8.25               | 4.24                 | 0.04          | 1.57             |


     12/11/2025 Forward Tests (The steering servomotor is held at 65 degrees):

| Average Overshoot  | Overshoot Dispersion | Drift Average | Drift Dispersion |
|--------------------|----------------------|---------------|------------------|
| -7.78              | 3.54                 | -0.07         | 2.44             |


Also, a 90-degree turn of the robot was tested without using the gyro sensor. These tests were done with the steering servo motor at 30 degrees and 135 speed. The distance was calculated using the encoder of the DC movement motor. The values are all in degrees.

    15/11/2025 Turn Tests (The steering servomotor is held at 30 degrees):
    
| Average Overshoot  | Overshoot Dispersion | Drift Average | Drift Dispersion |
|--------------------|----------------------|---------------|------------------|
| -23.37             | 10.75                | 71.63         | 10.75            |




**Update 12/09/2025: Inertial Measurement Unit (IMU) Replacement**

**Reason:** The previous IMU (GY-521) exhibited intermittent reading failures, characterized by a lock-up or saturation of its output values. This behavior caused inaccuracies in determining the vehicle's orientation, affecting the reliability of our test runs.

**Action:** The unit was replaced with a **GY-9250** model (which integrates the MPU-9250 and BMP280 sensors), selected for its reported higher stability and reliability.

**Replication Note:** For the purpose of replicating the base system, the previous sensor model is functional. This update addresses a specific need for robustness and precision during continuous operation.

![IMG-GY9250](/schemes/IMG-GY9250.jpg)

Power and Sensor Management
====

## Energy
The robot uses **six 3.7V 9900mAh (18650) batteries**. They are divided into **four battery holders connected in parallel** to manage current efficiently. The system's maximum voltage is **8.4V**.

To power our **Raspberry Pi 4 computer**, we use an **LM2596 voltage regulator set to 5.1V**. This allows the board to function correctly without needing to be plugged into a wall outlet. It's important to note that we initially conducted tests for obstacle management with the Pi connected to a wall outlet, which allowed us to validate our code's behavior. However, it was evident that we needed to solve this power issue for the robot to be fully autonomous.

![IMG_Baterias_3.7v](/schemes/IMG_Baterias_3.7v.webp)

## Ultrasonic Sensors
At the start of the season, our initial proposal was to use **VL53L0X laser sensors**. We thought they would be the best option due to their long range and precision, so our first chassis was designed for them. During testing, we realized their characteristics were problematic for us: the **conical field of view** caused the sensors to detect the floor, leading to many failures. Furthermore, using 3 of these sensors consumed an excessive amount of our microcontroller's memory (at that time, an Arduino UNO).

In our specific case, the decision to discard the laser sensors was primarily because we did not consider the points mentioned earlier. If at any point someone wishes to replicate this project, please note that with the necessary modifications, these sensors could be used and potentially yield better results than ours. As a recommendation, we can say that if others wish to use them, they should have a microcontroller with greater memory capacity, and the sensors should be positioned at least 5 cm above the ground.

![IMG_SensorLaser](/schemes/IMG_SensorLaser.jpg)

These issues led us to replace them with **HC-SR04 ultrasonic sensors**. We used these in the regional competition, and they remain in our current model. The change was straightforward thanks to our modular design; the mounts for the ultrasonic sensors we had designed for previous projects used the same mounting system as the laser sensor mounts on the chassis, making the transition quick and easy.

We have **3 ultrasonic sensors:** 1 at the front and 2 on the sides. The side sensors are primarily used to determine the turning direction. Once the robot leaves the first quadrant, there will always be one side with a barrier and one without. The sensor detecting the greater distance will dictate the turn direction (clockwise or counterclockwise).

The front sensor has multiple functions:

1. In the "Free Turn" challenge, it detects the distance to the wall before turning. Upon detecting 50 cm, the robot stops and proceeds to turn.

2. In the "Obstacles" challenge, it detects proximity to an obstacle. Upon detecting less than 15 cm, it stops and turns in the direction indicated by the color.

![IMG_SU](/schemes/IMG_SU.webp)

## HMI (Human-Machine Interface)
On the vehicle's third level, there is a mini protoboard (170 points) with **3 LEDs and a button**. A passive buzzer is also mounted on the chassis.

**Free Turn Challenge:** When the start button is pressed, an LED turns on and the buzzer plays a sound, indicating that the code is running.

**Obstacles Challenge:** Depending on the case detected by the camera, different LEDs light up to provide visual feedback, and the buzzer plays distinct sounds for each case.

**Note:** The buzzer also generates a sound whenever the ultrasonic sensors detect an object.

Obstacle Management
===
## Camera
For the obstacle challenge, we chose to use a **Raspberry Pi Camera rev.1.3.** This section only details how we mounted the camera on the vehicle and the models used. For information on the detection algorithm and the obstacle avoidance solution, please refer to the [src](/src) folder.

Before using the official Raspberry camera, we used the ***Night Vision Camera for Raspberry Pi - IR-CUT 5MP model.** As the name implies, it is a night vision camera. When testing it with OpenCV, the image had a red filter that prevented our color detection algorithm from working correctly. The color values varied drastically depending on the environment, requiring very specific lighting conditions, which was a major problem.

![IMG_Camera_VN](/schemes/IMG_Camara_VN.webp)

The solution was to switch to the standard Raspberry Pi camera. This change immediately resolved the issue, allowing the robot to function reliably in different environments. With this resolved, we proceeded to design and implement a custom mount for the camera, taking ideas from various designs for our solution.

![IMG_Camera_RB1.3](/schemes/IMG_Camara_RB1.3.webp)


## Position Control

*You are about to see all the information contained in our [src](/src/README.md). folder. If you wish to only view the information from this specific section, you can access it directly via the hyperlink (with the exception of the updates for the Panama Open Championship).*

**Encoder**

To determine how much the robot has moved, we use the encoder incorporated into our motor. Specifically, it is a HALL encoder: a device that uses **Hall** effect sensors to detect rotational movement and convert it into an electrical signal indicating position or speed.

**What is the** ***Hall effect?*** The Hall effect is the appearance of a potential difference (or electric field) transverse to an electrical conductor when an electric current flows through it and, simultaneously, a magnetic field is applied perpendicular to the direction of the current.

We implemented PID control software to make the robot advance the specific number of pulses we command. With the PID control, we ensure the robot does not fall significantly short of or exceed the target pulse count, thus achieving greater consistency in our vehicle's test runs. So far, we have managed to maintain the error within a range of ±4 pulses. This software is incorporated into both the Free Turn and the Obstacles challenges.

- [CTRL_POS](/src/CTRL_POS.ino).


**Gyro Sensor**

The use of this sensor is fundamental for our vehicle's operation. It has only two objectives, but they are very important:

1. Moving in a straight line **(Both challenges)**.
2. Make Turns.

**1. Straight-Line Movement:** To ensure the robot follows a trajectory without significant deviation, we use a PD control system to maintain the robot at the 0° angle of the gyroscope's Z-axis. We use a GY9250 Accelerometer/Gyroscope. To maintain the same angle, the control system adjusts the steering using the servo motor. This means that if the robot deviates, the steering system turns in the opposite direction to the deviation, allowing the robot to stay centered. As expected, it doesn't always stay perfectly parallel to the trajectory; over very long distances, there is a deviation of no more than 2 degrees, but it remains within our specified tolerances.

**2. Making Turns:** All turns the robot makes on the track are based on the gyro sensor. It's important to clarify that for making turns, the robot's steering moves to the maximum possible position (Left or Right). We do not adjust the steering to intermediate positions for turns, as this would introduce many more variables to consider. Once the steering is in position for the turn, the vehicle moves forward until it reaches the target angle indicated by the gyro sensor. When it stops, the steering returns to the center position, and the robot continues with its routine.


## Ultrasonic Sensors

**Front Sensor**

As mentioned in other sections of the repository, our robot is equipped with 3 ultrasonic sensors: one on the front and two on the sides (one per side). The front sensor has two main functions:

  1. To detect the distance between the robot and the track barriers **(Open Challenge).**
  2. To obtain the distance between the robot and the obstacles **(Obstacle Challenge).**

1. Open & Obstacle Challenge Barrier Detection: In both the Free Turn and Obstacles challenges, the robot uses the front ultrasonic sensor to detect when it is close to the barrier in front of it. When the distance reaches ≤ 64 cm, the vehicle stops and turns in the direction indicated by the side sensors. This allows us to maintain greater consistency when executing turns in both challenges.
   
2. Obstacle Challenge Proximity Sensing: In the Obstacle challenge, when the robot positions itself in front of an obstacle, the sensor detects the distance between the obstacle and the vehicle. This allows the robot to know its proximity to the obstacle and determine how wide or tight the turn needs to be.

he code for using the ultrasonic sensors is located in the [funciones](/src/S25_Obstaculos_29_08_2025/Funciones.ino) file. This file contains most of the functions we use in our main code.

**Side Sensors**

The side sensors serve several objectives in our vehicle, which are as follows:

1. Determine the direction of rotation (Clockwise/Counter-clockwise).
2. Wall following.
3. Detect the inner barriers.
4. Detect the parking boundaries.

Previously, the sensors shared the same port for the **Trigger** pin using a Y-cable. Initially, we did this because our microcontroller was an Arduino UNO, which saved ports, and we thought it was a good idea as it also simplified the code. However, due to our vehicle's needs and the additional sensors we integrated over time, the Arduino UNO had insufficient ports, forcing us to upgrade to an Arduino MEGA. Despite this change, we continued with the shared trigger configuration for the side sensors. Over time, we noticed the sensors exhibited erratic behavior or did not always return accurate distances. For this reason, leveraging the abundance of digital ports on the new microcontroller, we modified the setup so that each sensor now has its own dedicated trigger port.

The objectives of the side sensors are explained below:

1. **Turn Direction:** In both challenges, when the robot makes its first turn to head to another quadrant, we call the *twoSUS* function to get the distance detected by both sensors. Depending on which sensor detected the greater distance, we determine the turn direction for the run: if the right sensor had the greater distance, the direction is clockwise; if the left sensor had the greater distance, it is counter-clockwise.
   
2. **Wall Following:** In the Obstacle challenge, we use a PD follower to track the outer barriers in cases where there are one or two green objects in the quadrant (the development of the obstacle avoidance algorithm will be discussed later). After completing the quadrant with obstacles, the robot positions itself to detect the obstacles in the next obstacle quadrant.

3. **Inner Barrier Detection:** In cases involving at least one red object, the robot uses the side sensor facing the inner barriers to align with them. This provides greater control over the robot's position after avoiding the red obstacle. We previously also used wall following for scenarios involving red objects, but since the distance traveled was very short, the robot would end the following routine in inconsistent positions, making it very difficult to position itself for the next quadrant or obstacle.

4. **Parking Boundary Detection:** The name of this objective is self-explanatory. When the robot completes the three laps, we use one of the side sensors (depending on the turn direction) to detect one of the parking boundaries to initiate the parking routine.

   
## Obstacle Management 

**Algorithm**

The category rules mention 36 possible obstacle position cases. To solve this challenge, our team first discussed the best approach, as it's crucial to correctly define the problem before programming. After several hours of discussion, we arrived at the following solution: the position cases can be simplified to just 4 core scenarios:

- Red
- Green
- Green - Red
- Red - Green

Regardless of their specific location within the quadrant, all cases reduce to these 4 configurations. Therefore, our solution was to program routines for these 4 cases. After avoiding the obstacles in one quadrant, the robot positions itself to detect the obstacles in the next quadrant. This process repeats in a loop until the 3 laps are completed.

**Object and Color Detection Program**

Our camera program divides the screen's view into zones and highlights the contour of the obstacles, ignoring contours smaller than the area covered by the actual obstacles. It changes the value of a variable depending on the color of the obstacle detected in front of it and selects the case based on the color and position of the objects. To determine the position of the obstacles, we use the camera's pixels on the Y-axis; the case is determined by the pixel location and the color of the object's contour.

Since the robot uses both a Raspberry Pi and an Arduino, the camera sends these values to the Arduino via the serial port by printing the value and transmitting it. The Arduino then performs an action based on the received value. To provide a simpler visual confirmation of the detected case, the robot has 4 LEDs, and one lights up for each specific case detected. As an additional feedback mechanism, it also generates different sound frequencies depending on the case.

