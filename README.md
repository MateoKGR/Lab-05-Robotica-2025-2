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

    A([Inicio]) --> B[Configurar TRIS y periféricos]
    B --> C[Inicializar variables: estado, conteo, nled, etc.]
    C --> D[Configurar INT0, Timer0 y HLVD]
    D --> E{{Bucle while(1)}}

    E --> F[Leer boton (RC0)]
    F --> G[Mostrar RGB[nled] en LATE]
    G --> H[Leer botonReinicio (RB1) y botonEmergencia (RB0)]

    H --> I{{¿botonReinicio cambió?}}
    I -->|Sí| J[Actualizar estdorei]
    J --> K{{¿botonReinicio == 1?}}
    K -->|Sí| L[conteo = 0; nled = 0]
    K -->|No| M[Continuar]
    I -->|No| M

    M --> N{{¿estado == 1 y boton == 0?}}
    N -->|Sí| O[Delay antirrebote 20ms]
    O --> P{{¿RC0 sigue en 0?}}

    P -->|Sí| Q[conteo++]
    Q --> R{{¿conteo > 9?}}
    R -->|Sí| S[conteo=0; nled++; buzzer 100ms]
    S --> T{{¿nled > 6?}}
    T -->|Sí| U[nled=0; buzzer 300ms]
    T -->|No| V[Continuar]

    R -->|No| V

    V --> W[Esperar hasta que RC0 vuelva a 1]
    W --> X[Delay 20ms]
    P -->|No| Y[Continuar]

    X --> Z
    Y --> Z

    Z --> ZA[estado = boton]
    ZA --> ZB[LATD = conteo]
    ZB --> E
```
