# Librería de Control para Robot Diferencial (Raspberry Pi Pico + L298N)

**Archivo:** `direccionesCARRO3.ubl`

**Plataforma:** MicroBlocks

**Hardware Objetivo:** Raspberry Pi Pico con Driver L298N

## 📋 Descripción General

Este módulo proporciona una interfaz de alto nivel para controlar un chasis robótico de dos ruedas (tipo diferencial) utilizando una **Raspberry Pi Pico** y un controlador de puente H **L298N**.

La librería abstrae el control de los pines GPIO mediante funciones de movimiento (`Adelante`, `Retroceder`, `Giros`) y permite la ejecución de figuras geométricas autónomas.

## ⚙️ Hardware y Conexiones

El sistema está diseñado para funcionar con 4 pines de control digital conectados a las entradas lógicas del driver L298N.

### Variables de Configuración de Pines

Las funciones principales aceptan 4 argumentos que representan los pines GPIO de la Raspberry Pi Pico conectados al driver:

* **`MotIzqA` y `MotIzqB**`: Controlan el sentido de giro del Motor Izquierdo.
* **`MotDerA` y `MotDerB**`: Controlan el sentido de giro del Motor Derecho.

### Diagrama de Conexión Sugerido (Pines por defecto)

Basado en los valores predeterminados del código, la configuración recomendada es:

| Raspberry Pi Pico (GPIO) | Driver L298N | Función Lógica |
| --- | --- | --- |
| **GP10** | IN1 (o IN3) | `MotIzqA` (Motor Izquierdo Polo A) |
| **GP11** | IN2 (o IN4) | `MotIzqB` (Motor Izquierdo Polo B) |
| **GP12** | IN3 (o IN1) | `MotDerA` (Motor Derecho Polo A) |
| **GP13** | IN4 (o IN2) | `MotDerB` (Motor Derecho Polo B) |

> **Nota:** También se utilizan los siguientes pines auxiliares definidos en los bloques `Formas` y `ledEstado`:
> * **GP2 y GP6**: Entradas de sensores/interruptores para activar formas.
> * **GP14**: Salida PWM para LED de estado.
> 
> 

## 🎮 Lógica de Movimiento (Driver L298N)

El módulo controla el Puente H enviando señales lógicas `true` (High/3.3V) y `false` (Low/GND). A continuación se detalla la tabla de verdad implementada en el código:

### 1. Adelante

Mueve el vehículo hacia el frente activando la polaridad "positiva" en ambos motores.

* **Motor Izquierdo:** `MotIzqA` = LOW, `MotIzqB` = HIGH
* **Motor Derecho:** `MotDerA` = LOW, `MotDerB` = HIGH

### 2. Retroceder

Invierte la polaridad para mover el vehículo hacia atrás.

* **Motor Izquierdo:** `MotIzqA` = HIGH, `MotIzqB` = LOW
* **Motor Derecho:** `MotDerA` = HIGH, `MotDerB` = LOW

### 3. Parar

Detiene el flujo de corriente a los motores (modo inercia).

* **Todos los pines:** LOW (`false`).

### 4. Giros (Sobre su propio eje)

* 
**Giro Derecha:** El motor izquierdo avanza y el derecho retrocede.


* 
**Giro Izquierda:** El motor izquierdo retrocede y el derecho avanza.



---

## 📐 Funciones de Formas Geométricas

Estas funciones combinan movimientos y temporizadores (`waitMillis`) para realizar trayectorias predefinidas.

### `Cudrado` (Cuadrado)

Realiza una trayectoria cuadrada repitiendo 4 veces la secuencia:

1. Avanza durante 500ms.
2. Gira a la Derecha durante 500ms.


*(Nota: Los tiempos pueden requerir ajuste según la velocidad de tus motores y el voltaje de la batería)*.



### `Triangulo`

Realiza una trayectoria triangular alternando avances y giros a la izquierda con tiempos asimétricos para intentar cerrar la forma (700ms, 400ms, 750ms).

### `Formas` (Selector Automático)

Permite seleccionar la figura a dibujar mediante sensores o interruptores externos conectados a `pin1` y `pin2`.

* Si **Pin1 es HIGH** (y Pin2 LOW) -> Ejecuta **Cuadrado**.
* Si **Pin2 es HIGH** (y Pin1 LOW) -> Ejecuta **Triángulo**.
* 
*Requiere lógica de exclusión mutua (XOR implícito en los bloques `if`)*.



---

## 💡 Indicadores de Estado

### `ledEstado`

Controla un LED (conectado por defecto al GP14) para indicar si el robot está activo o en espera, utilizando PWM (Señal analógica simulada):

* **Activo (`true`):** Brillo máximo (Valor PWM 1023).
* 
**Inactivo (`false`):** Brillo mínimo/Dimmed (Valor PWM 50).



---

## 🚀 Cómo usar este módulo

1. Abre **MicroBlocks**.
2. Importa el archivo `direccionesCARRO3.ubl`.
3. Arrastra el bloque `Adelante` al área de script.
4. Verifica que los números de los pines en el bloque coincidan con tu cableado en la **Raspberry Pi Pico**.
5. Si una rueda gira en sentido contrario al esperado, intercambia los cables del motor en la bornera del L298N o invierte los números de los pines en la llamada de la función (ej. cambia 10 por 11).

---

### ¿Te gustaría que genere un script de ejemplo en MicroBlocks para probar los motores uno por uno y verificar el cableado?
