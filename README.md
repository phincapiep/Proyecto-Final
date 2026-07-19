# Proyecto-Final: Comedero para gatos

- Paula Cristina Hincapie Peña 1016942585
- Catalina Silva Fandiño 1014857784
- 21/07/2026
  
## Descripción
El presente proyecto consistió en el diseño e implementación de un comedero automático digital para gatos. Esta solución nace como respuesta a las dificultades que enfrentan las personas con jornadas laborales extensas o que viajan frecuentemente para mantener una rutina de alimentación adecuada para sus mascotas. La falta de regularidad en la alimentación de los gatos genera altos niveles de estrés, ansiedad y puede desencadenar enfermedades graves como la Hepatosis Lipídica. Para solucionar esto, se desarrolló un sistema digital basado en una FPGA que dosifica alimento seco en horarios preprogramados de manera automática, garantizando una dieta balanceada sin depender de la intervención humana diaria.
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
-  Servomotor para el mecanismo dispensador de alimento.
-   Módulo de visualización displays de 7 segmentos.
-   Cable USB como fuente de alimentación del sistema.
-   Interruptores (Switches) y Pulsadores (Botones) físicos de la FPGA.
-   Carcasa, armazón y recipiente de alimentación fabricados mediante impresión 3D.
-   Cables de conexión (jumpers) y alimento seco para gatos.  
## Marco Teórico

## Metodólogía 
El proyecto se desarrolló siguiendo un modelo secuencial e incremental:
Fase de Planificación: Se definieron los requerimientos técnicos, excluyendo intencionalmente el uso de microcontroladores y sensores externos para mantener el enfoque digital. 
Fase de Diseño Mecánico y Fabricación: Se buscó y analizó la estructura física del dispensador utilizando tecnología de impresión 3D, garantizando un soporte firme para el alimento y el servomotor.
Fase de Desarrollo Lógico: Se modularizó el sistema en Verilog, comenzando por las pruebas individuales del servomotor y los displays en protoboard, para luego desarrollar el reloj interno y la memoria de configuración.  
Fase de Integración y Pruebas: Se ensambló la electrónica dentro de la carcasa impresa en 3D. Finalmente, se realizaron pruebas de funcionamiento con alimento real para calibrar el tiempo de apertura de las compuertas y asegurar la estabilidad de la estructura bajo carga.  
## Código

## Resultados

## Conclusiones 
