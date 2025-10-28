# 📚 TAREA 6

---

## 1) Resumen

- **Nombre del proyecto:** _Alarmas_  
- **Equipo / Autor(es):** _Juan David García Cortéz y Sumie Arai Erazo_  
- **Curso / Asignatura:** _Sistemas embebidos 1_  
- **Fecha:** _09/04/25_  
- **Descripción breve:** _Crear 2 codigos: uno donde cada alarma del 0-4 controla un LED distinto con un periodo propio. Y modificar ping pong para aumentar o disminuir velocidad mediante 2 botones adicionales_


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

## 5) Códigos

### codigo 1 que debia de fincionar

```bash
#include "pico/stdlib.h"
#include "hardware/irq.h"
#include "hardware/structs/timer.h"
#include "hardware/gpio.h"

#define LED0_PIN     6   
#define LED1_PIN     7
#define LED2_PIN     8
#define LED3_PIN     9                     

#define ALARM0_NUM   0
#define ALARM1_NUM   1
#define ALARM2_NUM   2
#define ALARM3_NUM   3

#define ALARM0_IRQ   timer_hardware_alarm_get_irq_num(timer_hw, ALARM0_NUM)
#define ALARM1_IRQ   timer_hardware_alarm_get_irq_num(timer_hw, ALARM1_NUM)
#define ALARM2_IRQ   timer_hardware_alarm_get_irq_num(timer_hw, ALARM2_NUM)
#define ALARM3_IRQ   timer_hardware_alarm_get_irq_num(timer_hw, ALARM3_NUM)


// Próximos "deadlines" (32 bits bajos en µs) y sus intervalos en µs
static volatile uint32_t next0_us, next1_us, next2_us, next3_us;
static const uint32_t INTERVALO0_US = 250u;
static const uint32_t INTERVALO1_US = 400u;
static const uint32_t INTERVALO2_US = 500u;
static const uint32_t INTERVALO3_US = 800u;

// ISR para ALARM0
static void on_alarm0_irq(void) {
    hw_clear_bits(&timer_hw->intr, 1u << ALARM0_NUM);
    sio_hw->gpio_togl = 1u << LED0_PIN;
    next0_us += INTERVALO0_US;
    timer_hw->alarm[ALARM0_NUM] = next0_us;
}

// ISR para ALARM1
static void on_alarm1_irq(void) {
    hw_clear_bits(&timer_hw->intr, 1u << ALARM1_NUM);
    sio_hw->gpio_togl = 1u << LED1_PIN;
    next1_us += INTERVALO1_US;
    timer_hw->alarm[ALARM1_NUM] = next1_us;
}
// ISR para ALARM2
static void on_alarm2_irq(void) {
    hw_clear_bits(&timer_hw->intr, 1u << ALARM2_NUM);
    sio_hw->gpio_togl = 1u << LED2_PIN;
    next2_us += INTERVALO2_US;
    timer_hw->alarm[ALARM2_NUM] = next2_us;
}
// ISR para ALARM3
static void on_alarm3_irq(void) {
    hw_clear_bits(&timer_hw->intr, 1u << ALARM3_NUM);
    sio_hw->gpio_togl = 1u << LED3_PIN;
    next3_us += INTERVALO3_US;
    timer_hw->alarm[ALARM3_NUM] = next3_us;
}

int main() {

    gpio_init(LED0_PIN);
    gpio_set_dir(LED0_PIN, GPIO_OUT);
    gpio_put(LED0_PIN, 0);

    gpio_init(LED1_PIN);
    gpio_set_dir(LED1_PIN, GPIO_OUT);
    gpio_put(LED1_PIN, 0);

    gpio_init(LED2_PIN);
    gpio_set_dir(LED2_PIN, GPIO_OUT);
    gpio_put(LED2_PIN, 0);

    gpio_init(LED3_PIN);
    gpio_set_dir(LED3_PIN, GPIO_OUT);
    gpio_put(LED3_PIN, 0);

    // Timer de sistema en microsegundos (por defecto source = 0)
    timer_hw->source = 0u;

    uint32_t now_us = timer_hw->timerawl;

    // Primeros deadlines
    next0_us = now_us + INTERVALO0_US;
    next1_us = now_us + INTERVALO1_US;
    next2_us = now_us + INTERVALO2_US;
    next3_us = now_us + INTERVALO3_US;

    // Programa ambas alarmas
    timer_hw->alarm[ALARM0_NUM] = next0_us;
    timer_hw->alarm[ALARM1_NUM] = next1_us;
    timer_hw->alarm[ALARM2_NUM] = next2_us;
    timer_hw->alarm[ALARM3_NUM] = next3_us;

    // Limpia flags pendientes antes de habilitar
    hw_clear_bits(&timer_hw->intr, (1u << ALARM0_NUM) | (1u << ALARM1_NUM) | (1u << ALARM2_NUM) | (1u << ALARM3_NUM));

    // Registra handlers exclusivos para cada alarma
    irq_set_exclusive_handler(ALARM0_IRQ, on_alarm0_irq);
    irq_set_exclusive_handler(ALARM1_IRQ, on_alarm1_irq);
    irq_set_exclusive_handler(ALARM2_IRQ, on_alarm2_irq);
    irq_set_exclusive_handler(ALARM3_IRQ, on_alarm3_irq);

    // Habilita fuentes de interrupción en el periférico TIMER
    hw_set_bits(&timer_hw->inte, (1u << ALARM0_NUM) | (1u << ALARM1_NUM) | (1u << ALARM2_NUM) | (1u << ALARM3_NUM));

    // Habilita ambas IRQ en el NVIC
    irq_set_enabled(ALARM0_IRQ, true);
    irq_set_enabled(ALARM1_IRQ, true);
    irq_set_enabled(ALARM2_IRQ, true);
    irq_set_enabled(ALARM3_IRQ, true);

    // Bucle principal: todo el parpadeo ocurre en las ISRs
    while (true) {
        tight_loop_contents();
    }
}

```


## 6) Esquemático

<img src="..\recursos\imgs\pingopong.jpg" alt="esquematico 1" width="420">



## 7) Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/jyWKDoAtEeA" frameborder="0" allowfullscreen></iframe>