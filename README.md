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
    B[Configurar TRIS y periféricos]
    C[Inicializar variables]
    D[Configurar INT0, Timer0 y HLVD]
    E{{Bucle while(1)}}
    F[Leer boton (RC0)]
    G[Mostrar RGB[nled] en LATE]
    H[Leer botonReinicio y botonEmergencia]
    I{{¿botonReinicio cambió?}}
    J[Actualizar estdorei]
    K{{¿botonReinicio == 1?}}
    L[conteo = 0; nled = 0]
    M[Continuar]
    N{{¿estado == 1 y boton == 0?}}
    O[Delay antirrebote (20ms)]
    P{{¿RC0 sigue en 0?}}
    Q[conteo++]
    R{{¿conteo > 9?}}
    S[conteo=0; nled++; buzzer 100ms]
    T{{¿nled > 6?}}
    U[nled = 0; buzzer 300ms]
    V[Continuar]
    W[Esperar hasta que RC0 vuelva a 1]
    X[Delay 20ms]
    Z[estado = boton]
    ZA[LATD = conteo]

    A --> B --> C --> D --> E
    E --> F --> G --> H --> I

    I -->|Sí| J --> K
    K -->|Sí| L --> M
    K -->|No| M
    I -->|No| M

    M --> N
    N -->|Sí| O --> P
    P -->|Sí| Q --> R
    R -->|Sí| S --> T
    T -->|Sí| U --> V
    T -->|No| V
    R -->|No| V

    V --> W --> X --> Z --> ZA --> E
    P -->|No| Z
    N -->|No| Z

```
