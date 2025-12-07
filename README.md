# Lab-05-Robotica-2025-2
Laboratorio 5 de Robótica 2025-2s, realizado por Jeison Diaz y Mateo Ramos

# Integrantes
1. Jeison Nicolás Diaz Arciniegas [jediazar@unal.co](JeisonD0819)
2. Mateo Ramos Cujer [mramoscu@unal.edu.co](MateoKGR)

# Informe

Indice:
1. [Descripción detallada de la solución planteada](#descripcion)
2. [Diagrama de flujo de acciones del robot](#diagrama)
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

## Plano de planta
## Descripción de las funciones utilizadas

```mermaid

flowchart TD

    A([Inicio])
    B[Configurar TRIS y perifericos]
    C[Inicializar variables]
    D[Configurar INT0, Timer0 y HLVD]
    E{{Bucle principal}}
    F[Leer RC0]
    G[Actualizar LED RGB]
    H[Leer boton de reinicio y emergencia]

    I{{Cambio en botonReinicio}}
    J[Actualizar estdorei]
    K{{botonReinicio == 1}}
    L[Reiniciar conteo y nled]
    M[Continuar]

    N{{estado == 1 y boton == 0}}
    O[Antirrebote 20 ms]
    P{{RC0 sigue en 0}}
    Q[Incrementar conteo]
    R{{conteo > 9}}
    S[conteo=0; nled++; buzzer corto]

    T{{nled > 6}}
    U[nled=0; buzzer largo]
    V[Continuar]

    W[Esperar a que RC0 regrese a 1]
    X[Delay de estabilizacion 20 ms]
    Z[estado = boton]
    ZA[Actualizar display 7 segmentos]

    %% FLOW CONNECTIONS
    A --> B --> C --> D --> E
    E --> F --> G --> H --> I

    I -->|Si| J --> K
    K -->|Si| L --> M
    K -->|No| M
    I -->|No| M

    M --> N
    N -->|Si| O --> P
    P -->|Si| Q --> R
    R -->|Si| S --> T
    T -->|Si| U --> V
    T -->|No| V
    R -->|No| V

    V --> W --> X --> Z --> ZA --> E

    P -->|No| Z
    N -->|No| Z

```
## Grafica 2

```mermaid

flowchart TD

    A([Interrupcion])

    %% --- Parada de emergencia ---
    B{{INT0IF igual a 1}}
    C[Reset INT0IF y activar banderaEmergencia]
    D{{banderaEmergencia igual a 1}}
    E[Encender LED RGB en rojo]

    F{{TMR0IF igual a 1}}
    G[Reset TMR0IF, recargar TMR0 y alternar LED]
    H[Continuar]

    I[Leer botonReinicio]
    J{{botonReinicio igual a 1}}
    K[Desactivar banderaEmergencia]

    %% --- Timer normal ---
    L{{TMR0IF igual a 1 en modo normal}}
    M[Alternar LED RA1]

    %% --- Bajo voltaje HLVD ---
    O{{HLVDIF igual a 1}}
    P[Deshabilitar HLVD]
    R{{VDIRMAG igual a 0}}
    S[precarga = 3036 y VDIRMAG = 1]
    T[precarga = 49911 y VDIRMAG = 0]
    U[Habilitar HLVD y limpiar HLVDIF]

    V([Retornar de interrupcion])

    %% FLOW CONNECTIONS
    A --> B
    B -->|Si| C --> D
    D -->|Si| E --> F
    F -->|Si| G --> H
    F -->|No| H
    H --> I --> J
    J -->|Si| K --> L
    J -->|No| D

    B -->|No| L

    L -->|Si| M --> O
    L -->|No| O

    O -->|Si| P --> R
    R -->|Si| S --> U --> V
    R -->|No| T --> U --> V

    O -->|No| V

```