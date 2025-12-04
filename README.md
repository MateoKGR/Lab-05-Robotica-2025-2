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
## Diagrama de flujo de acciones del robot
## Plano de planta
## Descripción de las funciones utilizadas
## Gráfica digital

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
