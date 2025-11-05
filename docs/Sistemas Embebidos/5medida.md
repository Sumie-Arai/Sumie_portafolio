# 📚 TAREA 5

---

## 1) Resumen

- **Nombre del proyecto:** _Medida de error_  
- **Equipo / Autor(es):** _Juan David García Cortéz y Sumie Arai Erazo_  
- **Curso / Asignatura:** _Sistemas embebidos 1_  
- **Fecha:** _09/04/25_  
- **Descripción breve:** _Simular una onda sinusoidal con un filtro y observar el cambio en su frecuencia._


## 2) Objetivos

- **General:** _Utilizar diferentes formas de calcular frecuencia_
- **Específicos:**
  - _Observar el error en la frecuenciaa con timer del controlador_


## 3) Alcance y Exclusiones

- **Incluye:** _Osciloscopio, capacitor de 100mf, resistencias._
- **No incluye:** 
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


// Blink con timer (SDK alto nivel): cambia BLINK_MS para ajustar
#include "pico/stdlib.h"
#include "pico/time.h"

#define LED_PIN 6
static const int BLINK_MS = 100;  // <-- ajusta tu periodo aquí

bool blink_cb(repeating_timer_t *t) {
    static bool on = false;
    gpio_put(LED_PIN, on = !on);
    return true; // seguir repitiendo la alarma
}

int main() {
    stdio_init_all();

    gpio_init(LED_PIN);
    gpio_set_dir(LED_PIN, true);

    repeating_timer_t timer;
    // Programa una interrupción periódica cada BLINK_MS:
    add_repeating_timer_ms(BLINK_MS, blink_cb, NULL, &timer);

    while (true) {
        // El trabajo "pesado" debería ir aquí (no en la ISR).
        tight_loop_contents();
    }
}

```


## 6) Foto de osciloscpio

<div style="position: relative; width: 100%; height: 0; padding-top: 56.2500%;
 padding-bottom: 0; box-shadow: 0 2px 8px 0 rgba(63,69,81,0.16); margin-top: 1.6em; margin-bottom: 0.9em; overflow: hidden;
 border-radius: 8px; will-change: transform;">
  <iframe loading="lazy" style="position: absolute; width: 100%; height: 100%; top: 0; left: 0; border: none; padding: 0;margin: 0;"
    src="https://www.canva.com/design/DAG3Hn28N6M/xA94uctfAIGeZAaNUshl7g/view?embed" allowfullscreen="allowfullscreen" allow="fullscreen">
  </iframe>
</div>
<a href="https:&#x2F;&#x2F;www.canva.com&#x2F;design&#x2F;DAG3Hn28N6M&#x2F;xA94uctfAIGeZAaNUshl7g&#x2F;view?utm_content=DAG3Hn28N6M&amp;utm_campaign=designshare&amp;utm_medium=embeds&amp;utm_source=link" target="_blank" rel="noopener">Inicio</a> de Sumie Arai

