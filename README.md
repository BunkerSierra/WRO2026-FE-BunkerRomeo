# Equipo Bunker Romeo – WRO Future Engineers 2026

## Índice

1. [Acerca del Equipo](#acerca-del-equipo)
2. [Videos de la Competencia](#videos-de-la-competencia)
   - [Open Challenge](#open-challenge)
   - [Obstacle Challenge](#obstacle-challenge)
3. [Implementación de Pure Pursuit y Mejoras Estructurales del Robot](#implementación-de-pure-pursuit-y-mejoras-estructurales-del-robot)
   - [Funcionamiento del sistema Pure Pursuit (histórico, ya no vigente)](#funcionamiento-del-sistema-pure-pursuit-histórico-ya-no-vigente)
   - [Modificaciones estructurales del robot](#modificaciones-estructurales-del-robot)
     - [Footprint](#footprint)
   - [Implementación del sensor VL53L0X](#implementación-del-sensor-vl53l0x)
   - [Modificaciones en el sistema de alimentación](#modificaciones-en-el-sistema-de-alimentación)
   - [Modificaciones en el sistema de visión y hardware](#modificaciones-en-el-sistema-de-visión-y-hardware)
4. [Especificaciones del robot](#especificaciones-del-robot)
   - [Fotos del vehículo rev.15 (Regional Mexicali)](#fotos-del-vehículo-rev15-regional-mexicali)
5. [Incidente eléctrico del 25 de junio de 2026](#incidente-eléctrico-del-25-de-junio-de-2026)
6. [Sensor trasero de esquina y pruebas de vuelta abierta (28 de junio de 2026)](#sensor-trasero-de-esquina-y-pruebas-de-vuelta-abierta-28-de-junio-de-2026)
   - [Sensor para lectura de líneas de esquina](#sensor-para-lectura-de-líneas-de-esquina)
   - [Pruebas de la ronda de vuelta abierta](#pruebas-de-la-ronda-de-vuelta-abierta)
7. [Actualización Post-Regional de Baja California (5 de julio de 2026)](#actualización-post-regional-de-baja-california-5-de-julio-de-2026)
   - [Cambio de sensores laterales: de ultrasónico a VL53L0X ToF](#cambio-de-sensores-laterales-de-ultrasónico-a-vl53l0x-tof)
   - [Cambio de algoritmo de navegación en curvas: de giroscopio a seguimiento de muro](#cambio-de-algoritmo-de-navegación-en-curvas-de-alineación-por-giroscopio-a-seguimiento-de-muro)
8. [Cambio de algoritmo de evasión de obstáculos: de Pure Pursuit a seguimiento reactivo por distancia](#cambio-de-algoritmo-de-evasión-de-obstáculos-de-pure-pursuit-a-seguimiento-reactivo-por-distancia)
9. [Pruebas de evasión de obstáculos (7 de julio de 2026)](#pruebas-de-evasión-de-obstáculos-7-de-julio-de-2026)
10. [BOM (Bill of Materials)](#bom-bill-of-materials)

---

## Acerca del Equipo

Somos un equipo de Baja California, México, compuesto por dos integrantes: Ian Fernando Rivera Armenta y Jacobo Arteaga Castañeda, de Bunker Robotics. Ambos miembros del equipo cuentan con experiencia en otras competencias, específicamente en la categoría Robomission, sumando un total de 10 años de experiencia combinada.

Este es nuestro segundo año participando en la categoría Future Engineers. Comenzamos nuestra preparación al inicio del año (enero de 2025) y, con el paso del tiempo, nuestro robot ha pasado por varias iteraciones y mejoras.

![EQUIPOROMEO](/t-photos/EQUIPOROMEO.jpeg)


[⬆ Volver al índice](#índice)

---

## Videos de la Competencia

Conforme al reglamento oficial **WRO 2026 – Future Engineers**, cada equipo debe publicar un video en YouTube (público o accesible mediante enlace) que documente el manejo autónomo del vehículo para cada reto, con una duración mínima de 30 segundos por video.

| Reto | Estado | Enlace | Duración del recorrido |
|------|--------|--------|-------------------------|
| **Open Challenge** (Vuelta Abierta) | ✅ Publicado | [Ver en YouTube](https://youtu.be/jBpTh44YIUg) | 75 s |
| **Obstacle Challenge** (Vuelta con Obstáculos) | ✅ Publicado | [Ver en YouTube](https://youtu.be/mim8iLk7CLE) | Clip de prueba |

### Open Challenge

El video corresponde a la prueba de la ronda de vuelta abierta realizada el **28 de junio de 2026** (ver sección [Pruebas de la ronda de vuelta abierta](#pruebas-de-la-ronda-de-vuelta-abierta)). En la corrida documentada, el robot:

- Ejecuta de forma autónoma **3 vueltas consecutivas** sobre la pista.
- Se estaciona en el **cuadrante de inicio** del recorrido, sin intervención manual.
- Completa la totalidad del reto en **75 segundos**.

**Sistemas involucrados durante la corrida:** seguimiento de muro (*wall-following*) mediante sensores VL53L0X laterales para la toma de curvas, sensor infrarrojo MH Sensor Series en el extremo trasero inferior para lectura de líneas de esquina, y HuskyLens + Arduino Mega como unidad de control principal.

### Obstacle Challenge

El video corresponde a la prueba de evasión de obstáculos realizada el **7 de julio de 2026** (ver sección [Pruebas de evasión de obstáculos](#pruebas-de-evasión-de-obstáculos-7-de-julio-de-2026)). El clip muestra al robot recorriendo una **sección recta de la pista** y evadiendo, en orden, un pilar **rojo** seguido de un pilar **verde**:

- **Pilar rojo:** el robot lo mantiene de su lado derecho, conforme a la regla de tránsito del reto.
- **Pilar verde:** el robot lo mantiene de su lado izquierdo.

**Sistemas involucrados durante la corrida:** detección de color mediante la cámara HuskyLens, seguimiento del obstáculo manteniéndolo centrado en cámara a partir de 50 cm de distancia, giro de evasión a partir de 30 cm, y protocolo de re-centrado de 10 frames al perder de vista el obstáculo (ver sección [Cambio de algoritmo de evasión de obstáculos](#cambio-de-algoritmo-de-evasión-de-obstáculos-de-pure-pursuit-a-seguimiento-reactivo-por-distancia)).

[⬆ Volver al índice](#índice)

---

## Implementación de Pure Pursuit y Mejoras Estructurales del Robot

Durante esta revisión realizamos múltiples modificaciones tanto en el sistema de navegación como en la estructura física y electrónica del robot, con el objetivo de mejorar la estabilidad, reducir dimensiones y aumentar la capacidad de adaptación ante diferentes escenarios de competencia.

Una de las mejoras más importantes fue la implementación de un sistema de seguimiento de trayectoria basado en **Pure Pursuit**, utilizando inicialmente la cámara Raspberry Pi Rev 1.3 como sensor principal de percepción.

Anteriormente, nuestro sistema de evasión de obstáculos dependía principalmente de maniobras preprogramadas y casos específicos. Con la incorporación del algoritmo Pure Pursuit, el robot fue capaz de generar trayectorias dinámicas alrededor de los obstáculos detectados.

> **⚠️ Actualización:** El algoritmo Pure Pursuit descrito en esta sección fue posteriormente **eliminado y reemplazado** por un esquema de evasión reactiva basado en distancia, vigente desde, al menos, las pruebas de evasión de obstáculos del 7 de julio de 2026 (ver sección [Cambio de algoritmo de evasión de obstáculos: de Pure Pursuit a seguimiento reactivo por distancia](#cambio-de-algoritmo-de-evasión-de-obstáculos-de-pure-pursuit-a-seguimiento-reactivo-por-distancia)). **Ya no se generan waypoints ni trayectorias geométricas** para la evasión de obstáculos. Los sistemas de sensores para mantener distancia respecto a los muros y para la toma de vueltas **no cambiaron**. La siguiente descripción se conserva únicamente como referencia histórica del proceso de desarrollo.

### Funcionamiento del sistema Pure Pursuit (histórico, ya no vigente)

1. Se capturaban imágenes del entorno en tiempo real mediante la cámara.
2. Se utilizaba visión por computadora para detectar la posición relativa del obstáculo.
3. Dependiendo de la ubicación detectada, se generaban waypoints alrededor del objeto.
4. Estos waypoints formaban una trayectoria temporal que el robot debía seguir.
5. El controlador Pure Pursuit seleccionaba continuamente un punto adelantado sobre la trayectoria y calculaba el ángulo de dirección necesario para alcanzarlo.

Este enfoque permitía generar movimientos más suaves y naturales en comparación con sistemas basados únicamente en correcciones directas o maniobras fijas.

Además, la trayectoria se recalculaba constantemente mientras el obstáculo permanecía visible, lo que permitía corregir pequeñas variaciones en tiempo real y adaptarse dinámicamente a diferentes posiciones de los obstáculos.

La implementación de Pure Pursuit también redujo, en su momento, la complejidad del sistema, ya que únicamente se requería:

- detectar el obstáculo.
- generar waypoints alrededor de este.
- y seguir la trayectoria calculada geométricamente.

El algoritmo funcionaba calculando la curvatura necesaria para alcanzar un punto objetivo ubicado cierta distancia adelante del vehículo, conocido como **lookahead point**. Esto producía giros más suaves y estables durante la navegación.

### Modificaciones estructurales del robot

También realizamos modificaciones importantes en las dimensiones físicas del robot debido al cambio de ruedas instaladas en los extremos derecho e izquierdo del sistema.

#### Footprint

En la revisión anterior utilizamos ruedas de **62.4 mm × 20 mm**, las cuales ofrecían buena estabilidad, pero incrementaban el tamaño general del robot. Para esta temporada, sustituimos dichas ruedas por un nuevo modelo de **57 mm × 14 mm** de Lego Spike Prime, permitiéndonos reducir tanto la altura como el ancho de la estructura.

**¿Por qué se hizo este cambio?**
Se realizó porque las nuevas ruedas tienen un **coeficiente de desgaste más alto** que el de las otras llantas, lo que mejora la durabilidad y el agarre en superficies de competencia. Además, su menor tamaño contribuye a reducir dimensiones y peso.

#### Como resultado:

- Redujimos la altura total del robot de **23.9 cm** a **20.3 cm**.
- El ancho pasó de **15 cm** a **14.6 cm**.

Esta reducción nos permitió obtener una estructura más compacta y ligera, además de mejorar ligeramente el centro de gravedad del sistema, favoreciendo la estabilidad durante curvas y maniobras rápidas. El menor tamaño de las ruedas también contribuyó a optimizar la distribución interna de componentes electrónicos y cableado dentro del chasis.

### Implementación del sensor VL53L0X

Otro cambio importante fue la incorporación del **sensor láser VL53L0X** dentro del sistema electrónico del robot.

En temporadas anteriores únicamente habíamos utilizado este sensor para pruebas experimentales, por lo que no formaba parte de nuestro sistema final. Sin embargo, durante esta edición logramos integrarlo exitosamente dentro de la arquitectura principal del robot.

El sensor VL53L0X utiliza tecnología **Time-of-Flight (ToF)** para medir distancias mediante luz infrarroja, ofreciendo mediciones más precisas y estables que los sensores ultrasónicos convencionales.

La implementación de este sensor tenía como propósito:

- mejorar la detección frontal de obstáculos.
- aumentar la precisión de medición a corta distancia.
- y complementar la información obtenida mediante visión por computadora.

> **Nota:** Debido a limitaciones de tiempo durante el proceso de integración y calibración, decidimos **no utilizar el sensor durante la competencia regional**. A pesar de ello, consideramos que su implementación representa una mejora importante para futuras iteraciones del robot.
>
> **Actualización (5 de julio de 2026):** Tras el Regional de Baja California, este sensor pasó de ser experimental a formar parte permanente de la arquitectura del robot.

### Modificaciones en el sistema de alimentación

También realizamos cambios importantes en el sistema de alimentación eléctrica del robot con el objetivo de reducir espacio, simplificar la distribución energética y disminuir el peso total de la estructura.

En la edición pasada utilizamos un arreglo compuesto por **seis baterías de 3.7V**, conectadas para obtener aproximadamente **12V y 20000mAh**. Aunque esta configuración cumplía con los requerimientos energéticos del sistema, ocupaba una cantidad considerable de espacio dentro del robot y aumentaba significativamente el peso general de la plataforma.

Para esta edición, reemplazamos el sistema por únicamente **dos baterías de 7.8V y 12200mAh**, permitiéndonos:

- simplificar el sistema de energía.
- reducir el espacio ocupado por las baterías.
- mejorar la distribución interna de componentes.
- y disminuir el peso total del robot.

**¿Por qué se hizo este cambio?**
Se realizó para **liberar espacio interno, reducir el peso y simplificar la gestión energética**. La configuración anterior era voluminosa y pesada; el nuevo arreglo mantiene suficiente autonomía con una fracción del tamaño y masa.

Además, recientemente comenzamos a realizar pruebas utilizando un **segundo conjunto de baterías de 7.4V y 3000mAh**. Estas pruebas tienen como objetivo evaluar alternativas más ligeras y compactas que nos permitan reducir aún más el peso del robot y mejorar la eficiencia energética del sistema.

Aunque estas baterías poseen una menor capacidad de almacenamiento, ofrecen ventajas importantes en términos de tamaño, distribución interna y reducción de carga sobre la estructura del robot.

Actualmente continuamos realizando pruebas comparativas entre ambas configuraciones de alimentación para determinar cuál ofrece el mejor equilibrio entre:

- autonomía.
- estabilidad eléctrica.
- peso.
- y rendimiento general durante la competencia.

Gracias a todas estas modificaciones, logramos desarrollar un robot más compacto, eficiente y adaptable, tanto a nivel mecánico como electrónico.

### Modificaciones en el sistema de visión y hardware

Durante esta edición realizamos cambios importantes en el sistema de visión y en el hardware principal del robot, con el objetivo de simplificar la arquitectura electrónica y mejorar la detección de obstáculos durante la navegación autónoma.

**Anteriormente**, utilizábamos una **Raspberry Pi** junto con una **cámara Raspberry Pi** como sistema principal de procesamiento y percepción visual. Esta configuración ofrecía resultados funcionales en tareas básicas de visión por computadora, pero incrementaba la complejidad general del sistema y requería una mayor carga de procesamiento para ejecutar los algoritmos de detección.

**El cambio a HuskyLens** se realizó el **21 de mayo de 2026**. Decidimos reemplazar tanto la cámara Raspberry Pi como la propia Raspberry Pi por una **cámara HuskyLens**, utilizando únicamente el **Arduino Mega** como controlador principal.

#### Razones del cambio:

- **Facilidad de implementación**: la HuskyLens tiene funciones integradas de visión artificial y modos preconfigurados de reconocimiento y seguimiento de objetos, lo que simplifica enormemente el desarrollo del sistema de detección y evasión de obstáculos.
- **Menor carga de procesamiento**: al delegar la visión artificial a la HuskyLens, el Arduino Mega queda liberado para tareas de control y navegación, mejorando la capacidad de reacción durante maniobras dinámicas.

#### Propósitos de la nueva implementación:

- simplificar la arquitectura electrónica del robot.
- reducir la complejidad del procesamiento de visión artificial.
- mejorar la detección y seguimiento de obstáculos en tiempo real.
- disminuir la carga de procesamiento del sistema general.
- y optimizar la capacidad de reacción del robot durante maniobras dinámicas.

> **Estado actual:** La cámara HuskyLens ya ha sido integrada al robot (desde el 21 de mayo de 2026). Se continuará realizando pruebas y calibraciones para evaluar el rendimiento de los diferentes modos de reconocimiento de la cámara y determinar la configuración más adecuada para las condiciones de competencia.

[⬆ Volver al índice](#índice)

---

## Especificaciones del robot

### Fotos del vehículo rev.15 (Regional Mexicali)

| Vista | Foto |
|-------|------|
| Vista Frontal | ![VistaFrontal](v-photos/VistaFrontal.jpeg) |
| Vista Lateral Izquierda | ![VistaLateraLizq](v-photos/VistaLateraLizq.jpeg) |
| Vista Trasera | ![VistaTrasera](v-photos/VistaTrasera.jpeg) |
| Vista Lateral Derecha | ![VistaLateralder](v-photos/VistaLateralder.jpeg) |
| Vista Superior | ![VistaSuperior](v-photos/VistaSuperior.jpeg) |
| Vista Inferior | ![VistaInferior](v-photos/VistaInferior.jpeg) |

[⬆ Volver al índice](#índice)

---

## Incidente eléctrico del 25 de junio de 2026

El día **25 de junio de 2026** se presentó una falla eléctrica en el robot durante las pruebas: tanto el **regulador de voltaje** como el **servomotor de dirección (MG90S)** sufrieron un corto circuito y resultaron dañados (quemados).

Como consecuencia, las pruebas de navegación se detuvieron temporalmente mientras se diagnosticaba el origen de la falla.

**Estado actual:** el equipo se encuentra en proceso de **reemplazar ambos componentes** y de revisar el cableado y las conexiones asociadas, con el objetivo de evitar que la falla se repita antes de retomar las pruebas y continuar con la siguiente competencia.

[⬆ Volver al índice](#índice)

---

## Sensor trasero de esquina y pruebas de vuelta abierta (28 de junio de 2026)

### Sensor para lectura de líneas de esquina

El día **28 de junio de 2026** se incorporó un nuevo **sensor infrarrojo MH Sensor Series** (basado en el sensor óptico TCRT5000 con comparador LM393) ubicado en el **extremo trasero inferior** del robot. Su propósito es permitir que, al momento de tomar una vuelta, el robot pueda **leer las líneas de la esquina** de la pista.

**¿Por qué se hizo este cambio?**
Hasta ese momento, el robot no contaba con un sensor dedicado exclusivamente a detectar las líneas ubicadas en las esquinas de la pista, lo que podía generar imprecisiones al iniciar o finalizar una maniobra de giro. Con este nuevo sensor infrarrojo, el robot obtiene una referencia adicional para identificar con mayor exactitud su posición dentro de la esquina, complementando la información proporcionada por los sensores laterales VL53L0X y el algoritmo de seguimiento de muro.

### Pruebas de la ronda de vuelta abierta

Ese mismo día, **28 de junio de 2026**, se realizaron pruebas de la **ronda de vuelta abierta (Open Round)**. Durante estas pruebas, el robot mostró un desempeño constante:

- Completó **3 vueltas** de forma consistente.
- Se estacionó correctamente en el **cuadrante en el que había iniciado** el recorrido.
- Todo el recorrido se completó dentro de **75 segundos**.

Estos resultados validan, en conjunto, las mejoras implementadas hasta la fecha (sensores VL53L0X laterales, seguimiento de muro y el nuevo sensor de esquina), reflejando un avance importante en la estabilidad y repetibilidad del sistema de navegación.

🎥 **Video de evidencia:** [Ver corrida completa en YouTube](https://youtu.be/jBpTh44YIUg) — ver también sección [Videos de la Competencia](#videos-de-la-competencia).

[⬆ Volver al índice](#índice)

---

## Actualización Post-Regional de Baja California (5 de junio de 2026)

A partir del **Regional de Baja California**, comenzamos a implementar una serie de cambios tanto en el hardware como en el algoritmo de navegación del robot, con el objetivo de mejorar la precisión durante las maniobras de evasión y el seguimiento de muros dentro de la pista.


### Cambio de sensores laterales: de ultrasónico a VL53L0X ToF

Anteriormente, los sensores ubicados en los **laterales** del robot eran de tipo ultrasónico (HC-SR04P). A partir de esta actualización, sustituimos dichos sensores por **sensores láser VL53L0X** de tipo **Time-of-Flight (ToF)**.

**¿Por qué se hizo este cambio?**
Se realizó con el objetivo de obtener **mayor precisión al momento de detectar la distancia entre el muro y el robot**. Los sensores ultrasónicos, al depender de la reflexión de ondas sonoras, son más susceptibles a variaciones de lectura según el ángulo o el material del muro; en cambio, los sensores VL53L0X, al basarse en luz infrarroja, ofrecen mediciones más estables y consistentes a corta distancia.

Vale la pena señalar que este sensor ya había sido probado de forma experimental anteriormente (ver sección [Implementación del sensor VL53L0X](#implementación-del-sensor-vl53l0x)), pero no se había integrado de forma definitiva debido a limitaciones de tiempo durante el regional previo. Con esta actualización, el VL53L0X pasa a formar parte permanente de la arquitectura electrónica del robot, ahora utilizado específicamente en los laterales.

### Cambio de algoritmo de navegación en curvas: de alineación por giroscopio a seguimiento de muro

Este cambio de hardware nos permitió replantear también el algoritmo utilizado para tomar las vueltas dentro de la pista.

**Antes:** dependíamos del sensor giroscópico (GY-9250) para alinear al robot durante el giro, utilizando la medición de orientación angular para corregir la trayectoria.

**Ahora:** con la incorporación de los sensores VL53L0X en los laterales, el robot ya no depende del giroscopio para este propósito. En su lugar, el sistema mide constantemente la **distancia entre el robot y el muro exterior**, y ajusta la dirección para mantener una distancia consistente respecto a dicho muro mientras avanza por la curva. En otras palabras, migramos de un esquema de alineación angular (giroscopio) hacia un esquema de **seguimiento de muro (wall-following)** basado en distancia.

Actualmente nos encontramos en fase de pruebas con este nuevo enfoque, ajustando parámetros de control para lograr un seguimiento estable del muro exterior en diferentes configuraciones de pista.

[⬆ Volver al índice](#índice)

---

## Cambio de algoritmo de evasión de obstáculos: de Pure Pursuit a seguimiento reactivo por distancia

Se eliminó el uso del algoritmo **Pure Pursuit** para la evasión de obstáculos. Es importante aclarar que **únicamente** se sustituyó este componente: los sensores y el algoritmo utilizados para mantener distancia respecto a los muros y para la toma de vueltas (ver sección [Cambio de algoritmo de navegación en curvas](#cambio-de-algoritmo-de-navegación-en-curvas-de-alineación-por-giroscopio-a-seguimiento-de-muro)) **no cambiaron**.

### Funcionamiento del nuevo esquema de evasión de obstáculos

1. El robot avanza en línea recta sobre la pista.
2. Al detectar un obstáculo a **50 cm** de distancia, inicia su seguimiento, ajustando la dirección para mantenerlo **centrado en el campo de visión de la cámara HuskyLens**.
3. Al llegar a **30 cm** de distancia del obstáculo, el robot comienza a girar para esquivarlo.
4. El giro continúa hasta que el obstáculo **deja de ser detectado** por la cámara.
5. Una vez perdido de vista, se ejecuta un **protocolo de revisión de 10 frames**, durante el cual el robot se re-centra respecto al carril antes de continuar en línea recta.

A diferencia de Pure Pursuit, que generaba *waypoints* y calculaba una trayectoria curva geométrica alrededor del obstáculo, este esquema es puramente **reactivo**: se basa únicamente en dos umbrales de distancia (50 cm / 30 cm) y en mantener centrado el obstáculo detectado dentro del campo visual de la cámara, sin planeación de trayectoria previa.

> **Nota:** Este cambio afecta exclusivamente al sistema de evasión de obstáculos (Obstacle Challenge). Los sistemas de sensores VL53L0X para mantener distancia respecto a los muros y el esquema de seguimiento de muro (*wall-following*) para tomar las vueltas permanecen sin cambios.

[⬆ Volver al índice](#índice)

---

## Pruebas de evasión de obstáculos (7 de julio de 2026)

El día **7 de julio de 2026** se realizaron pruebas de **evasión de obstáculos** correspondientes al reto Obstacle Challenge.

El video documentado muestra una corrida sobre una **sección recta de la pista**, en la que el robot detecta y evade, en orden, un **pilar rojo** y posteriormente un **pilar verde**:

- Al detectar el **pilar rojo**, el robot ajusta su trayectoria para mantenerlo de su lado **derecho**.
- Al detectar el **pilar verde**, el robot ajusta su trayectoria para mantenerlo de su lado **izquierdo**.

Ambas maniobras se resuelven mediante el nuevo esquema de evasión reactiva basado en distancia: detección de color a través de la cámara HuskyLens, seguimiento del obstáculo manteniéndolo centrado en cámara a partir de 50 cm, giro de evasión a partir de 30 cm, y protocolo de re-centrado de 10 frames al perder de vista el obstáculo (ver sección [Cambio de algoritmo de evasión de obstáculos](#cambio-de-algoritmo-de-evasión-de-obstáculos-de-pure-pursuit-a-seguimiento-reactivo-por-distancia)).

🎥 **Video de evidencia:** [Ver corrida en YouTube](https://youtu.be/mim8iLk7CLE) — ver también sección [Videos de la Competencia](#videos-de-la-competencia).

> **Estado actual:** esta prueba corresponde a un tramo recto de la pista. Continuamos con pruebas adicionales para validar la evasión de pilares en distintas posiciones y combinaciones de color a lo largo del circuito completo.

[⬆ Volver al índice](#índice)

---

## BOM (Bill of Materials)

| Componente | Requerimiento de energía | Imagen | Precio |
|------------|--------------------------|--------|--------|
| Arduino Mega 2560 | 0.25-0.5W | ![Arduino](schemes/ArduinoMega.jpg) | ≈ 24.46 Dlls |
| Motor DC con Encoder: GA37-520 300RPM | 5.55-16.65W | ![Motor DC](schemes/MOTORDC.jpg) | ≈ 22.12 Dlls |
| Puente H TB6612FNG | 0.025W | ![Puente H](schemes/HBRIDGE.jpg) | ≈ 4.35 Dlls |
| Servo Motor: MG90S | 0.5-2.5W | ![Servo](schemes/SERVO.webp) | ≈ 4.08 Dlls |
| Sensor Ultrasonico: HC-SR04P x1 (frontal) | 0.075W | ![Ultrasonico](schemes/ULTRASONICO.webp) | ≈ 0.90 Dlls |
| Sensor Láser ToF: VL53L0X x2 (laterales) | ≈ 0.10W | ![Laser](schemes/laser.jpg) | ≈ 9.00 Dlls |
| Acelerometro/Giroscopio: GY-9250 | 0.033W | ![Giroscopio](schemes/GIRO.jpg) | ≈ 9.24 Dlls |
| LED x4 | 0.264W | ![LED](schemes/LED.png) | ≈ 0.44 Dlls |
| Buzzer | N/A | ![Buzzer](schemes/BUZ.jpg) | ≈ 0.27 Dlls |
| SEN0336 HuskyLens PRO OV5640 | 3.3~5.0V | ![HuskyLens](schemes/HUSKY.webp) | ≈ 40.65 Dlls |
| Sensor Infrarrojo (Line Tracking): MH Sensor Series x1 (trasero inferior) | 0.05-0.075W | ![Infrarrojo](schemes/MH_IR.jpg) | ≈ 1.50 Dlls* |
| **Total** | | | **≈ 117.50 Dlls*** |

[⬆ Volver al índice](#índice)
