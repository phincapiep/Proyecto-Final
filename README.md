# Proyecto-Final: Comedero para gatos

- Paula Cristina Hincapie Peña 1016942585
- Catalina Silva Fandiño 1014857784
- 21/07/2026
  
## Descripción
El presente proyecto consistió en el diseño e implementación de un comedero automático digital para gatos. Esta solución nace como respuesta a las dificultades que enfrentan las personas con jornadas laborales extensas o que viajan frecuentemente para mantener una rutina de alimentación adecuada para sus mascotas. La falta de regularidad en la alimentación de los gatos genera altos niveles de estrés, ansiedad y puede desencadenar enfermedades graves como la Hepatosis Lipídica. Para solucionar esto, se desarrolló un sistema digital con ayuda de los conocimientos adquiridos durante todo el semestre basado en una FPGA que dosifica alimento seco en horarios preprogramados de manera automática, garantizando una dieta balanceada sin depender de la intervención humana diaria.
## Objetivos
### Objetivo General
Diseñar e implementar un comedero automático digital para gatos que dosifique alimento seco en horarios programados, con el fin de garantizar una alimentación regular y prevenir problemas de salud asociados al estrés y al ayuno prolongado.  
### Objetivos Específicos
- Diseñar y construir la estructura física del comedero que garantice el almacenamiento adecuado y el flujo correcto del alimento seco.
-  Integrar componentes electrónicos al sistema tales como la FPGA para controlar el contador y servomotor, un sistema de visualización de estado, y una fuente de alimentación por cable USB.
-  Programar el sistema a través de Verilog para gestionar los horarios fijos de alimentación y calcular la cantidad de alimento dispensado según el tiempo de activación del servomotor.
-  Realizar pruebas de funcionamiento del sistema integrado para verificar la precisión del temporizador, la dosificación de porciones y la estabilidad del armazón.
## Materiales
-  Tarjeta de desarrollo FPGA.
-  Servomotor estándar adaptado mecánicamente a rotación continua (360°) para el mecanismo dispensador de alimento.
-   Módulo de visualización displays de 7 segmentos.
-   Cable USB como fuente de alimentación del sistema.
-   Interruptores (Switches) y Pulsadores (Botones) físicos de la FPGA.
-   Carcasa, armazón y recipiente de alimentación fabricados mediante impresión 3D.
-   Cables de conexión (jumpers) y alimento seco para gatos.  
## Marco Teórico

## Metodólogía 
El proyecto se desarrolló siguiendo un modelo secuencial e incremental:

El diagrama de bloques planteado fue el siguiente:

<img width="952" height="215" alt="image" src="https://github.com/user-attachments/assets/ef4a3017-9902-45c7-911d-af25bcb66a43" />



### Fase de Planificación: 
Se definieron los requerimientos técnicos, excluyendo intencionalmente el uso de microcontroladores y sensores externos para mantener el enfoque digital.


### Fase de Diseño Mecánico y Fabricación: 
Se buscó y analizó la estructura física del dispensador utilizando tecnología de impresión 3D, garantizando un soporte firme para el alimento y el servomotor. Además, para lograr una dispensación de alimento basada en el tiempo de giro y no en un ángulo fijo, se tomó un servomotor estándar de 180° y se modificó para obtener rotación continua. Esto se logró retirando el tope mecánico de los engranajes y sustituyendo el potenciómetro interno por un divisor de voltaje fijo compuesto por dos resistencias de igual valor, engañando al circuito de control interno para que gire continuamente al recibir un pulso de 2.0 ms y se detenga completamente al recibir el pulso de equilibrio de 1.5 ms.


En la siguiente tabla se resumen los cambios físicos y lógicos aplicados al motor:

| Característica | Servomotor Original (180°) | Servomotor Modificado (Rotación Continua 360°) |
| :--- | :--- | :--- |
| **Tope Mecánico** | Presente (limita el ángulo de giro). | **Eliminado** (permite el giro libre). |
| **Sensor Interno** | Potenciómetro (mide el ángulo). | **2 Resistencias fijas** (engañan al controlador). |
| **Pulso PWM: 1.5 ms**| Se mueve a la posición central (90°).| **Punto de equilibrio:** El motor se detiene y frena. |
| **Pulso PWM: 2.0 ms**| Se mueve al extremo máximo (180°). | **Giro constante:** Avanza a velocidad máxima. |

### Fase de Desarrollo Lógico: 
Se modularizó el sistema en Verilog, comenzando por las pruebas individuales del servomotor y los displays en protoboard, para luego desarrollar el reloj interno y la memoria de configuración.

Se creó primero el reloj 24 horas para tener la base del proyecto, se planteó el siguiente diagrama de bloques para esta parte: 


<div align="center">
  <figure>
    <img width="943" height="150" alt="image" src="https://github.com/user-attachments/assets/ef9c536c-0acb-4be5-8ec7-c2b7bb813a45" />
    <figcaption>Figura 1: Diagrama de bloques 1</figcaption>
  </figure>
</div>

Cuyos módulos no hay que modificar para desarrollar la parte de comparación con la hora configurada.

#### 1. top_module (Integración Principal)
Este es el módulo de mayor jerarquía arquitectónica. Su función no es procesar lógica, sino actuar como el "esqueleto" del sistema. Aquí se instancian y se interconectan mediante cables internos todos los demás módulos. Además, es el encargado de recibir las señales físicas de la FPGA (como el reloj de 50 MHz, los botones y los switches) y enrutar las señales de salida hacia los actuadores físicos (servomotor, LEDs y displays de 7 segmentos). Se incluye lógica de inversión (assign seg = ~seg_raw;) para adaptar las señales a la configuración de ánodo/cátodo común de la FPGA.

#### 2. DivFreqN (Divisor de Frecuencia)

| Parámetro | Detalle                  |
|----------|--------------------------|
| Entradas  | CLK, rst_n         |
| Salidas  | clk_div |

Dado que la FPGA opera con un reloj interno de muy alta velocidad (50 MHz), no se puede usar esa señal directamente para contar segundos o controlar el motor. Este módulo cuenta los ciclos del reloj de la FPGA y genera un pulso equivalente a un solo ciclo de reloj cuando alcanza un límite (N). En el proyecto, se instanció dos veces:

Una vez dividiendo a 50.000.000 para obtener una frecuencia de 1 Hz (un pulso cada segundo exacto), usado como la base de tiempo del reloj principal.

Una vez dividiendo a 50.000 para obtener una frecuencia de 1 kHz (un pulso cada milisegundo), utilizado para el barrido de los displays.

Para saber el parámetro en cada caso, se utilizó la siguiente fórmula:

$$
N = \frac{50 000 000}{f_{out}}
$$

#### 3. time_counter (Reloj Principal en BCD)

| Parámetro | Detalle                  |
|----------|--------------------------|
| Entradas  | CLK, rst_n, ena_1hz        |
| Salidas  | sec_u [3:0], sec_d [3:0], min_u [3:0], min_d [3:0], hour_u [3:0], hour_d [3:0] |

Este módulo es el que transforma los segundos en minutos y horas. Utiliza el pulso de 1 Hz para incrementar un contador digital estructurado en formato BCD (Separando así las decenas y unidades para mostrar en el display). Cuenta con registros independientes de 4 bits para las unidades y decenas de los segundos, minutos y horas. Incorpora la lógica combinacional para los desbordamientos de un reloj de 24 horas: al llegar a 9 en las unidades, incrementa las decenas; al llegar a 59 segundos/minutos, incrementa el bloque siguiente; y al detectar las 23:59:59, se reinicia de manera síncrona a 00:00:00.

Para traducir esta lógica al código, se estructuraron las condiciones (if-else) siguiendo un estricto orden de dependencia en cascada:

seg_u → seg_d → min_u → min_d → hour_u → hour_d

La evaluación ocurre desde la unidad que cambia con mayor frecuencia (seg_u) hacia la más lenta (hour_d). Si el registro actual no ha llegado a su límite de desbordamiento, el sistema simplemente le suma 1 (mediante la rama else) y finaliza el proceso. Solo cuando un registro alcanza su tope, se reinicia a 0 y "habilita" la evaluación del siguiente nivel, garantizando que el incremento de las horas y minutos ocurra en el ciclo de reloj exacto.

#### 4. bcd_to_7seg (Decodificador de Visualización)

| Parámetro | Detalle                  |
|----------|--------------------------|
| Entradas  | bcd [3:0]       |
| Salidas  | seg [6:0] |

Es un bloque de lógica combinacional. Su única función es tomar un número binario de 4 bits (de 0 a 9) entrante y utilizar una estructura case para determinar qué segmentos específicos (a, b, c, d, e, f, g) deben encenderse en un display físico para formar el carácter numérico correspondiente. Se debe tener en cuenta que la FPGA trabaja en ánodo común, por lo que se envía un 0 lógico al segmento a encender. La estructura de un display 7 segmentos es la siguiente: 

<div align="center">
  <figure>
    <img width="943" height="375" alt="image" src="https://github.com/user-attachments/assets/3bfba2f8-92bb-4d03-ac67-f5fedaf15d05" />
    <figcaption>Figura 1: Diagrama de bloques 1</figcaption>
  </figure>
</div>

Siendo esta la que se tiene en cuenta a la hora de definir cada caso (0-9)

#### 5. display_mux (Multiplexor Dinámico de Displays)

| Parámetro | Detalle                  |
|----------|--------------------------|
| Entradas  | CLK, rst_n, ena_1khz, sec_u [3:0], sec_d [3:0], min_u [3:0], min_d [3:0], hour_u [3:0], hour_d [3:0]  |
| Salidas  | current_bcd [3:0], display_sel[5:0] |

Para mostrar los 6 dígitos del reloj sin agotar los pines de salida de la FPGA, se implementa una técnica de multiplexación en el tiempo. Utilizando la señal de 1 kHz, el módulo escanea los displays encendiendo solo uno a la vez. En cada paso de esta rápida secuencia, el multiplexor enruta el dígito BCD correspondiente (proveniente del time_counter) hacia el único decodificador de 7 segmentos, activando de manera síncrona el pin de habilitación de dicho display. Al rotar entre los seis dígitos a una velocidad de mil veces por segundo crea la ilusión óptica de que todos los números están encendidos de forma constante y simultánea.

#### 6. button_edge_detect (Antirrebote y Detector de Flanco)

| Parámetro | Detalle                  |
|----------|--------------------------|
| Entradas  | CLK, rst_n, ena_1khz, btn_in  |
| Salidas  | btn_pulse |

Los interruptores mecánicos generan rebotes eléctricos al ser accionados, lo que la FPGA interpretaría como múltiples pulsaciones erróneas. Para solucionar esto, el módulo implementa un acondicionamiento de señal en tres etapas. Primero, utiliza un sincronizador de dos niveles para alinear la entrada con el reloj de la FPGA y evitar la metaestabilidad. Luego, aplica un filtro antirrebote (debouncer) mediante un contador secuencial que exige que la señal se mantenga ininterrumpidamente estable durante 10 milisegundos antes de registrarla como válida.

Finalmente, el módulo incorpora un detector de flanco de subida para evitar lecturas repetidas. Este bloque garantiza que el sistema emita un único pulso limpio de un ciclo de reloj, independientemente de cuánto tiempo el usuario mantenga accionado el botón. A nivel lógico, esto se logra mediante una compuerta AND que compara el estado validado actual con la negación de su estado en el ciclo anterior, asegurando que se genere exactamente un evento de disparo por cada interacción física.

#### 7. config_memory (Memoria de Horarios y Máquina de Estados)

| Parámetro | Detalle                  |
|----------|--------------------------|
| Entradas  | CLK, rst_n, btn_pulse, sw_in [4:0] |
| Salidas  | hora_desayuno [4:0], hora_almuerzo [4:0], hora_comida [4:0], leds_estado [2:0], config_done |

Este módulo gestiona la programación del comedero. Funciona como una Máquina de Estados Finitos (FSM) que transita entre cuatro estados: ESTADO_DESAYUNO, ESTADO_ALMUERZO, ESTADO_COMIDA y ESTADO_LISTO. Al recibir el pulso limpio del botón (confirmación), lee el estado actual de los 5 switches físicos, verifica que el valor sea menor a 24, y lo guarda en registros internos. Concluido el proceso, emite una señal config_done para indicarle al sistema que la programación ha finalizado y el comedero puede empezar a operar automáticamente.

El diagrama de estados representativo es el siguiente:

<div align="center">
  <figure>
    <img width="1254" height="1061" alt="image" src="https://github.com/user-attachments/assets/98e57b7a-241a-46ba-8f05-4cb94ce9462c" />
    <figcaption>Figura 1: Diagrama de bloques 1</figcaption>
  </figure>
</div>

<img width="1254" height="1061" alt="image" src="https://github.com/user-attachments/assets/98e57b7a-241a-46ba-8f05-4cb94ce9462c" />


#### 8. feeder_control (Lógica de Activación y Dosificación)

| Parámetro | Detalle                  |
|----------|--------------------------|
| Entradas  | CLK, rst_n, ena_1hz, config_done, sec_u [3:0], sec_d [3:0], min_u [3:0], min_d [3:0], hour_u [3:0], hour_d [3:0] ,hora_desayuno [4:0], hora_almuerzo [4:0], hora_comida [4:0] |
| Salidas  | motor_activa |

Este es el comparador principal. Constantemente convierte la hora actual del reloj (que está en formato BCD) a un formato binario de 5 bits para poder compararla con los horarios guardados por el usuario. Cuando las horas coinciden perfectamente y los minutos y segundos están en cero exacto (00:00), el módulo dispara la orden de alimentación. Inmediatamente, inicia una cuenta interna secundaria de 5 segundos. Durante este tiempo, mantiene la salida motor_active en alto, logrando una dosificación precisa basada en tiempo sin necesidad de incorporar sensores adicionales.

#### 9. servo_pwm (Controlador Modulador del Servomotor)

| Parámetro | Detalle                  |
|----------|--------------------------|
| Entradas  | CLK, rst_n, ena_1hz, config_done, sec_u [3:0], sec_d [3:0], min_u [3:0], min_d [3:0], hour_u [3:0], hour_d [3:0] ,hora_desayuno [4:0], hora_almuerzo [4:0], hora_comida [4:0] |
| Salidas  | motor_activa |

Los servomotores no funcionan con corriente continua simple, sino mediante Modulación por Ancho de Pulsos (PWM). Este módulo crea un ciclo de trabajo estándar de 20 milisegundos. Dentro de esa ventana de tiempo, si recibe la orden de alimentación (motor_active en alto), genera un pulso en alto constante de 2.0 milisegundos para obligar al servomotor a girar y dejar caer la comida. Cuando está en reposo, genera un pulso de 1.5 milisegundos, lo que mantiene al motor en su punto muerto (frenado), evitando que el peso del alimento abra las compuertas por gravedad.


### Fase de Integración y Pruebas: 
Se ensambló la electrónica dentro de la carcasa impresa en 3D. Finalmente, se realizaron pruebas de funcionamiento con alimento real para calibrar el tiempo de apertura de las compuertas y asegurar la estabilidad de la estructura bajo carga.  




## Resultados


## Uso de la IA

En este proyecto se usó la herramienta de IA de Gemini para organizar y estructurar de mejor manera los módulos, al hacerlos manualmente estaban desordenados y no se entendían facilmente, la IA los estructuraba de mejor manera y cambiaba los nombres de las variables para que se entendiera que era cada una. También se usó para revisar errores en los códigos, al dar errores de compilación, corregía puntuaciones o demás errores comunes al programar.

## Conclusiones 

- Todas las metas y restricciones propuestas en la fase de planeación se cumplieron satisfactoriamente.
- Se logró un funcionamiento lógico 100% basado en hardware mediante la FPGA, demostrando que es posible crear un sistema automatizado complejo sin la intervención de microcontroladores.
-  La carcasa impresa en 3D demostró ser el complemento mecánico ideal, soportando el peso del alimento real sin comprometer la estabilidad del sistema y permitiendo un anclaje seguro del servomotor.
-   El mecanismo de dosificación basado en el tiempo de activación del servomotor (5 segundos de giro) probó ser una alternativa eficaz y precisa, eliminando por completo la necesidad de implementar sensores de peso complejos.
-    Los subsistemas de multiplexación visual y la gestión de memoria para los horarios funcionaron de manera estable, permitiendo al usuario monitorear el reloj y configurar la dieta de la mascota de forma intuitiva.
-    El proyecto ofrece una solución tangible y económica a la problemática del estrés felino y la alimentación irregular durante la ausencia prolongada de los cuidadores.  
