# 📚 TAREA 4

---

## 1) Resumen

- **Nombre del proyecto:** _Ping Pong_  
- **Equipo / Autor(es):** _Juan David García Cortéz y Sumie Arai Erazo_  
- **Curso / Asignatura:** _Sistemas embebidos 1_  
- **Fecha:** _09/04/25_  
- **Descripción breve:** _Se reutiliza el código de barrido de la tarea 2 para convertirla en un juego de Ping Pong que incluye dos botones, uno por jugador, donde para rebotar la pelota se debe de presionar el  boton del jugador en el momento en el que se prenda el link de la ezquina del jugador. Al iniciar el jugador que pique el boton primero es quien "lanza" la pelota yendo la direccion hacia el jugador contrario, al ganar un jugador, durante las siguiente ronda la direccion iniciará hacia este._


## 2) Objetivos

- **General:** _Usar IRQs_
- **Específicos:**
  - _Crear una ISR_
  - _Usar cantidades peligrosas de LEDs TuT_
  - _Documentar progreso en página de GitHub_

## 3) Alcance y Exclusiones

- **Incluye:** _5 LEDs, 2 botones, un pi pico 2, programa en C SDK._
- **No incluye:** _Resistencias :b._

---

## 4) Requisitos

**Software**
- _SO compatible (Windows/Linux/macOS)_
- _Python 3.x / visual studio / raspberry pi pico._
- _"pico/stdlib.h", "hardware/structs/sio.h"_

**Conocimientos previos**
- _Programación básica en C_
- _Electrónica básica_
- _Git/GitHub_

---

## 5) Código

```bash
// tarea4.c
#include "pico/stdlib.h"
#include "hardware/gpio.h"

// Pines
#define P1 4
#define P2 5
#define WIN1 6
#define LED1 7
#define LED2 8
#define LED3 9
#define LED4 10
#define LED5 11
#define WIN2 12

// Flags de interrupción
volatile bool flag_p1 = false;
volatile bool flag_p2 = false;

// Función para parpadear LED ganador
void parpadear_led(uint led) {
    for (int i = 0; i < 3; i++) {
        gpio_put(led, 1);
        sleep_ms(200);
        gpio_put(led, 0);
        sleep_ms(200);
    }
}

// Callback de interrupción
void gpio_callback(uint gpio, uint32_t events) {
    if (events & GPIO_IRQ_EDGE_RISE) {
        if (gpio == P1) flag_p1 = true;
        else if (gpio == P2) flag_p2 = true;
    }
}

int main() {
    // Inicializar LEDs
    const uint32_t LED_MASK = (1u << WIN1) | (1u << LED1) | (1u << LED2) | (1u << LED3) |
                               (1u << LED4) | (1u << LED5) | (1u << WIN2);
    gpio_init_mask(LED_MASK);
    gpio_set_dir_masked(LED_MASK, LED_MASK);

    // Inicializar botones
    gpio_init(P1); gpio_set_dir(P1, GPIO_IN); gpio_pull_down(P1); //tambien tiene pulldown externo
    gpio_init(P2); gpio_set_dir(P2, GPIO_IN); gpio_pull_down(P2);
    gpio_set_irq_enabled_with_callback(P1, GPIO_IRQ_EDGE_RISE, true, &gpio_callback);
    gpio_set_irq_enabled(P2, GPIO_IRQ_EDGE_RISE, true);

    int ultima_direccion = 1; // 1: derecha (jugador 2), -1: izquierda (jugador 1)
    bool primer_jugada = true; // <--- NUEVO

    while (true) {
        gpio_put_masked(LED_MASK, 0); // Apagar todos los LEDs
        gpio_put(LED3, 1); // Encender LED3 del medio

        // Esperar a que se presione cualquier botón antes de iniciar la secuencia
        while (gpio_get(P1) == 0 && gpio_get(P2) == 0) {
            sleep_ms(10);
        }

        // Apagar LED3 antes de iniciar la secuencia
        gpio_put(LED3, 0);

        int direccion_inicial;
        if (primer_jugada) {
            // Primera jugada: dirección hacia el jugador que NO presionó el botón
            if (gpio_get(P1) == 1) {
                direccion_inicial = 1;  // P1 presionó, va hacia P2
            } else if (gpio_get(P2) == 1) {
                direccion_inicial = -1; // P2 presionó, va hacia P1
            } else {
                direccion_inicial = ultima_direccion; // Por si acaso
            }
            primer_jugada = false;
        } else {
            // Siguientes jugadas: dirección hacia el ganador anterior
            direccion_inicial = ultima_direccion;
        }

        int posicion = LED3;
        int direccion = direccion_inicial;
        flag_p1 = false;
        flag_p2 = false;

        int prev_p1 = 1, prev_p2 = 1;

        while (1) {
            gpio_put_masked(LED_MASK, 0);

            if (posicion >= LED1 && posicion <= LED5) {
                gpio_put(posicion, 1);
            }
            sleep_ms(400);

            int curr_p1 = gpio_get(P1);
            int curr_p2 = gpio_get(P2);

            if (posicion == LED1 && prev_p1 == 1 && curr_p1 == 0) {
                direccion = 1;
            } else if (posicion == LED5 && prev_p2 == 1 && curr_p2 == 0) {
                direccion = -1;
            }

            prev_p1 = curr_p1;
            prev_p2 = curr_p2;

            // Verificar victoria
            if (posicion == WIN1) {
                gpio_put_masked(LED_MASK, 0);
                parpadear_led(WIN2);
                ultima_direccion = 1; // Ahora la siguiente ronda va hacia jugador 2
                break;
            } else if (posicion == WIN2) {
                gpio_put_masked(LED_MASK, 0);
                parpadear_led(WIN1);
                ultima_direccion = -1; // Ahora la siguiente ronda va hacia jugador 1
                break;
            }

            posicion += direccion;
        }
        sleep_ms(500); // Espera antes de reiniciar el juego
    }
}

```


## 6) Esquemático

<img src="..\recursos\imgs\pingopong.jpg" alt="esquematico 1" width="420">



## 7) Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/jyWKDoAtEeA" frameborder="0" allowfullscreen></iframe>