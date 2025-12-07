# Lab-05-Robotica-2025-2
Laboratorio 5 de Robótica 2025-2s, realizado por Jeison Diaz y Mateo Ramos

# Integrantes
1. Jeison Nicolás Diaz Arciniegas [jediazar@unal.co](JeisonD0819)
2. Mateo Ramos Cujer [mramoscu@unal.edu.co](MateoKGR)

# Informe

Indice:
1. [Descripción detallada de la solución planteada](#descripcion)
2. [Diagramas digitales y DH utilizado](#diagramas)
3. [Plano de planta](#plano_planta)
4. [Descripción de las funciones utilizadas](#funciones)
5. [Gráfica digital](#grafica)


## Descripción detallada de la solución planteada
Se desarrolló un nodo de ROS 2 en Python que permite:

Comunicación directa con los motores Dynamixel mediante puerto serial.
Control individual y conjunto de cada articulación del manipulador.
Publicación del mensaje `/joint_states` para animación del modelo en RViz.
Implementación de una interfaz gráfica con control articular, control numérico, visualización en RViz y cálculo de la posición del TCP mediante cinemática directa.

La arquitectura del sistema integra tres componentes principales:
1. Nodo ROS 2 de control de motores.
2. Interfaz gráfica (GUI) en Tkinter.
3. Modelo del robot visualizado en RViz.

*Funciones utilizadas*
Control articular por sliders: Permite mover cada articulación del robot en tiempo real mediante deslizadores, respetando los límites físicos de los motores.
Control articular por ingreso numérico: Permite ingresar directamente valores de posición para cada motor.
Visualización en RViz: Se sincroniza el robot físico con el modelo virtual del PhantomX Pincher X100 usando `robot_state_publisher`.
Cinemática directa (TCP): Se calcula la posición del TCP utilizando parámetros DH y se muestran las coordenadas X, Y y Z en tiempo real.
Rutinas predefinidas: Se implementaron rutinas de movimiento que reproducen las poses solicitadas en el laboratorio.

## Diagramas digitales y DH utilizado
La cinemática directa se implementó usando parámetros Denavit-Hartenberg medidos directamente del robot:

| i | θᵢ | dᵢ | aᵢ | αᵢ |
|---|----|----|----|----|
| 1 | q1 | L1 | 0  | -π/2 |
| 2 | q2 | 0  | L2 | 0 |
| 3 | q3 | 0  | L3 | 0 |
| 4 | q4 | 0  | 0  | π/2 |

A continuación los diagramas utilizados.

![Diagrama DH](images/dh.jpg)

Visualización completa en MATLAB

![MATLAB](images/dhcompleto.jpg)
![alt text](images/dhcompleto2.jpg)

A continuación los diagramas digitales de las diferentes poses.

![Diagrama digital pose 1](images/diagramadig.jpg)
![Diagrama digital pose 2](images/diagramadig2.png)

## Diagrama de flujo de acciones del robot

```md
```mermaid
flowchart TD
    Inicio["Inicio del sistema"]
    Inicializar_ROS["Inicializar ROS 2"]
    Conectar_Motores["Conectar motores Dynamixel"]
    Interfaz_GUI["Interfaz gráfica"]
    Mover_Motor["Enviar comandos a motores"]
    Publicar_Joint_States["Publicar /joint_states"]
    RViz["RViz + robot_state_publisher"]
    Actualizar_TCP["Actualizar TCP (FK)"]

    Inicio --> Inicializar_ROS
    Inicializar_ROS --> Conectar_Motores
    Conectar_Motores --> Interfaz_GUI
    Interfaz_GUI --> Mover_Motor
    Mover_Motor --> Publicar_Joint_States
    Publicar_Joint_States --> RViz
    RViz --> Actualizar_TCP
```
```mermaid
flowchart TD
    Inicio["Inicio del sistema"]
    Inicializar_ROS["Inicializar ROS 2"]
    Conectar_Motores["Conectar motores Dynamixel"]
    Interfaz_GUI["Interfaz gráfica"]
    Mover_Motor["Enviar comandos a motores"]
    Publicar_Joint_States["Publicar /joint_states"]
    RViz["RViz + robot_state_publisher"]
    Actualizar_TCP["Actualizar TCP (FK)"]

    Inicio --> Inicializar_ROS
    Inicializar_ROS --> Conectar_Motores
    Conectar_Motores --> Interfaz_GUI
    Interfaz_GUI --> Mover_Motor
    Mover_Motor --> Publicar_Joint_States
    Publicar_Joint_States --> RViz
    RViz --> Actualizar_TCP
```
## Videos 
A continuación el video donde se demuestra el brazo alcanzando cada posición solicitada y la demostración de uso de la interfaz de usuario.

- [Video – Ejecución de poses del robot y demostración de la interfaz gráfica](videos/demostraciones.mp4)

## Plano de planta
## Código
Esta sección describe el código desarrollado para el laboratorio, el cual permite el control del manipulador Phantom X Pincher, el cálculo de la cinemática directa y la visualización del estado del robot mediante RViz y una interfaz gráfica.

El archivo que contiene el código completo del sistema es:

```text
src/pincher_controller_gui.txt
```
1. Importación de librerías
```
import rclpy
from rclpy.node import Node
from dynamixel_sdk import PortHandler, PacketHandler
import tkinter as tk
from tkinter import ttk, messagebox
import threading
import subprocess
import math
import numpy as np
```
Estas librerías permiten la creación del nodo ROS 2, la comunicación con los motores Dynamixel, la construcción de la interfaz gráfica de usuario, la ejecución de RViz y la realización de los cálculos matemáticos necesarios para la cinemática directa.

2. Selección del tipo de motor Dynamixel
```
USE_XL430 = False
```
Esta variable define el modelo de motor Dynamixel utilizado. Dependiendo de su valor, el código adapta automáticamente el protocolo de comunicación, las direcciones de memoria y los rangos de operación.

3. Funciones auxiliares de comunicación

```
def write_goal_position(packet, port, dxl_id, position):
def write_moving_speed(packet, port, dxl_id, speed):
def read_present_position(packet, port, dxl_id):
```
Estas funciones encapsulan la lectura y escritura de registros en los motores Dynamixel. Su objetivo es abstraer las diferencias entre protocolos y simplificar el envío de comandos a los actuadores.

4. Nodo ROS 2 – PincherController
```
class PincherController(Node):
```
Esta clase define el nodo principal del sistema. Se encarga de inicializar la comunicación serial con los motores, configurar los parámetros operativos, publicar los estados articulares, calcular la cinemática directa y gestionar la parada de emergencia.

5. Parámetros ROS configurables
```
self.declare_parameter('port', '/dev/ttyUSB0')
self.declare_parameter('baudrate', 1000000)
self.declare_parameter('dxl_ids', [1, 2, 3, 4, 5])
```
Estos parámetros permiten modificar la configuración del sistema sin necesidad de alterar el código fuente, utilizando el sistema de parámetros de ROS 2.

6. Publicación de estados articulares
```
self.joint_state_pub = self.create_publisher(
    JointState, '/joint_states', 10)
```
El nodo publica periódicamente el estado de las articulaciones en el tópico /joint_states, lo que permite sincronizar el robot físico con su modelo virtual en RViz.

7. Conversión entre valores Dynamixel y radianes
```
def dxl_to_radians(self, dxl_value):
def radians_to_dxl(self, radians):
```
Estas funciones convierten los valores de posición de los motores Dynamixel a radianes y viceversa, facilitando los cálculos cinemáticos y la correcta visualización del estado del robot.

8. Cálculo de la cinemática directa
```
def dh_transform(self, a, alpha, d, theta):
```
Esta función calcula la matriz de transformación homogénea utilizando los parámetros Denavit–Hartenberg para cada articulación del robot.
```
def update_tcp_position(self):
```
A partir de la matriz homogénea total se obtienen las coordenadas cartesianas X, Y y Z del TCP, las cuales se muestran en tiempo real en la interfaz gráfica.

9. Interfaz gráfica de usuario (GUI)
```
class PincherGUI:
```
La interfaz gráfica fue desarrollada utilizando Tkinter y organizada en varias pestañas que permiten el control articular, la visualización del TCP y la ejecución de RViz.

10. Integración con RViz
```
ros2 launch phantomx_pincher_description display.launch.py
```
Desde la interfaz gráfica es posible ejecutar RViz y visualizar el modelo tridimensional del manipulador sincronizado con el robot real.

11. Parada de emergencia
```
def emergency_stop(self):
```
La parada de emergencia desactiva el torque de todos los motores y bloquea el envío de nuevos comandos, garantizando la seguridad del sistema y del usuario.