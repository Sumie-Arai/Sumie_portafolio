
# 📚 TAREA 9

## 1) Resumen

- **Nombre del proyecto:** _Señales con ADC_  
- **Equipo / Autor(es):** _Juan David García Cortéz y Sumie Arai Erazo_  
- **Curso / Asignatura:** _Sistemas embebidos 1_  
- **Fecha:** _22/10/25_  
- **Descripción breve:** _Realizar dos códigos, uno de control de una fotoresistencia y otro de control de un servomotor con potenciómetro_


## 2) Objetivos

- **General:** _Aprender las utilidades del ADC_
- **Específicos:**
  - _Observar la resolución y el ruido en el ADC de la pico_


## 3) Alcance y Exclusiones

- **Incluye:** 
  - _1 servomotor_
  - _1 potenciómetro_
  -_1 resistencia variable de luz._
  -resistencias de 1K y una fuente._

 


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

## 5) Código de luz 

```bash

#include <stdio.h>
#include "pico/stdlib.h"
#include "hardware/adc.h"
 
int main() {
    stdio_init_all();
 
    // Inicializar ADC
    adc_init();
 
    // Elegir pin GP26 -> ADC0
    adc_gpio_init(26);
    adc_select_input(0);
 
    while (true) {
        uint16_t valor_adc = adc_read(); // Valor de 0 a 4095 (12 bits)
       
        // Calcular porcentaje (0% en 10, 100% en 4095)
        float porcentaje = 0.0f;
        if (valor_adc > 10) {
            porcentaje = ((valor_adc - 10) * 100.0f) / (4095.0f - 10.0f);
            if (porcentaje > 100.0f)
                porcentaje = 100.0f; // Limitar por seguridad
        }
 
        printf("Porcentaje: %.2f%%\n", porcentaje);
        sleep_ms(200);
    }
}
 
```

## 5) Código de servo


```bash
según chat este es para el servo pero ps kien sabe si jale #include <stdio.h>
#include "pico/stdlib.h"
#include "hardware/adc.h"
#include "hardware/pwm.h"

#define SERVO_PIN 15
#define ADC_PIN 26

// Ajusta según los valores que obtuviste
#define ADC_MIN 240
#define ADC_MAX 4030

int main() {
    stdio_init_all();

    // Inicializar ADC
    adc_init();
    adc_gpio_init(ADC_PIN);
    adc_select_input(0);

    // Configurar PWM para el servo
    gpio_set_function(SERVO_PIN, GPIO_FUNC_PWM);
    uint slice_num = pwm_gpio_to_slice_num(SERVO_PIN);
    pwm_set_wrap(slice_num, 24999);  // 50 Hz con reloj base 125 MHz / 100 = 1.25 MHz
    pwm_set_clkdiv(slice_num, 100.0f);
    pwm_set_enabled(slice_num, true);

    while (true) {
        uint16_t valor_adc = adc_read();

        // Limitar rango
        if (valor_adc < ADC_MIN) valor_adc = ADC_MIN;
        if (valor_adc > ADC_MAX) valor_adc = ADC_MAX;

        // Regla de 3: mapear ADC_MIN→1ms, ADC_MAX→2ms
        float pulso_us = 1000.0f + (valor_adc - ADC_MIN) * (1000.0f / (ADC_MAX - ADC_MIN));

        // Convertir a duty cycle (20 ms periodo → 20000 µs)
        uint16_t nivel_pwm = (uint16_t)((pulso_us / 20000.0f) * 24999);

        pwm_set_gpio_level(SERVO_PIN, nivel_pwm);

        printf("ADC: %u -> %.1f us -> Nivel PWM: %u\n", valor_adc, pulso_us, nivel_pwm);
        sleep_ms(100);
    }
}
```

## 6) Esquemáticos

<iframe 
  src="https://wokwi.com/projects/446817926756467713" 
  width="800" 
  height="400" 
  allowfullscreen>
</iframe>

_Nota: El led será el fotoresistor ya que no existe el componente en wokwi
<iframe 
  src="https://wokwi.com/projects/446818621127736321" 
  width="800" 
  height="400" 
  allowfullscreen>
</iframe>


## 7) Videos

<iframe width="560" height="315" src="https://www.youtube.com/embed/1XyUdpuH5Gg" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/zImjOQUQME8" frameborder="0" allowfullscreen></iframe>
