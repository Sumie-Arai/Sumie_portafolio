# 📚 TAREA 7

---

## 1) Resumen

- **Nombre del proyecto:** _Control de PWM_  
- **Equipo / Autor(es):** _Juan David García Cortéz y Sumie Arai Erazo_  
- **Curso / Asignatura:** _Sistemas embebidos 1_  
- **Fecha:** _30/09/25_  
- **Descripción breve:** _Se crean tres códigos distintos, uno para controlar un motor DC con 3 velocidades, otro para crear frecuencias que formen una melodía con un buzzer, y otro para _


## 2) Objetivos

- **General:** _Aprender las utilidades del PWM_
- **Específicos:**
  - _Crear una supuesta variacion de voltaje mediante variaciones de duty cycles_
  - _Utilizar frecuencias específicas para hacer notas musicales_
  - _Crear una señal sinusoidal a parir de una PWM y un filtro con capacitor y leerla con un osciloscopio_

## 3) Alcance y Exclusiones

- **Incluye:** 
  - _1 capacitor de 1uF, resistencias de 1k y 680 ohms, osciloscopio_
  -_1 buzzer pasivo, 1 resistencia de 220._
  -2 botones, resistencias pulldown de 1k, 1 motor DC._

 


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

**Control de velocidad de motor**

```bash
#include "pico/stdlib.h"
#include "hardware/pwm.h"

#define ENA 2     // PWM para velocidad
#define IN1 3     // Dirección
#define IN2 4     // Dirección
#define BTN_UP 14   // Botón para subir velocidad
#define BTN_DOWN 15 // Botón para bajar velocidad

#define F_PWM_HZ 2000
#define TOP 1023

// Velocidades en porcentaje
const uint8_t speed_percent[] = {40, 60, 80}; 
int speed_index = 1; // Comienza en velocidad media

// Guardar estado previo de botones
bool last_up = true;
bool last_down = true;

// Función: establece velocidad con "kickstart" si es necesario
void set_motor_speed(uint slice, uint chan, uint8_t percent) {
    uint16_t level = (percent * TOP) / 100;

    if (percent > 0 && percent <= 40) {
        // Arranque forzado al 100% por 100 ms
        pwm_set_chan_level(slice, chan, TOP);
        sleep_ms(100);
    }

    pwm_set_chan_level(slice, chan, level);
}

int main() {
    stdio_init_all();

    // Configurar pines de dirección (gira siempre en un sentido)
    gpio_init(IN1); gpio_set_dir(IN1, GPIO_OUT); gpio_put(IN1, 1);
    gpio_init(IN2); gpio_set_dir(IN2, GPIO_OUT); gpio_put(IN2, 0);

    // Configurar botones con resistencias pull-up internas
    gpio_init(BTN_UP); gpio_set_dir(BTN_UP, GPIO_IN); gpio_pull_up(BTN_UP);
    gpio_init(BTN_DOWN); gpio_set_dir(BTN_DOWN, GPIO_IN); gpio_pull_up(BTN_DOWN);

    // Configurar PWM en pin ENA
    gpio_set_function(ENA, GPIO_FUNC_PWM);
    uint slice = pwm_gpio_to_slice_num(ENA);
    uint chan  = pwm_gpio_to_channel(ENA);

    float f_clk = 125000000.0f; // clock de la Pico
    float div = f_clk / (F_PWM_HZ * (TOP + 1));
    pwm_set_clkdiv(slice, div);
    pwm_set_wrap(slice, TOP);

    // Iniciar en velocidad media
    set_motor_speed(slice, chan, speed_percent[speed_index]);
    pwm_set_enabled(slice, true);

    while (true) {
        bool up_now = gpio_get(BTN_UP);
        bool down_now = gpio_get(BTN_DOWN);

        // Detectar flanco de bajada con debounce
        if (last_up && !up_now) {
            sleep_ms(50); // debounce
            if (!gpio_get(BTN_UP)) {
                if (speed_index < 2) speed_index++;
                set_motor_speed(slice, chan, speed_percent[speed_index]);
            }
        }

        if (last_down && !down_now) {
            sleep_ms(50); // debounce
            if (!gpio_get(BTN_DOWN)) {
                if (speed_index > 0) speed_index--;
                set_motor_speed(slice, chan, speed_percent[speed_index]);
            }
        }

        last_up = up_now;
        last_down = down_now;

        sleep_ms(10);
    }
}

```

**Frecuencias de una melodía reconocible**

```bash
#include "pico/stdlib.h"
#include "hardware/pwm.h"

#define BUZZER_PIN 2
#define TOP 2048

// Notas de TU melodía
#define FAs 740.0f
#define RE  587.0f  
#define SI  494.0f
#define MI  659.0f
#define SOLs 831.0f
#define LA  880.0f
#define DOs 988.0f

// Duraciones
#define CORCHEA 214 //duración de una nota en ms, corchea es nota musical 
#define SILENCIO 214  // Los espacios son silencios de 214ms
#define SILENCIO_MENOR 50 //Silencio de 50ms

void tocar_nota(uint slice, uint chan, float frecuencia, int duracion) {
    if (frecuencia > 1.0f) {
        float f_clk = 125000000.0f;
        float div = f_clk / (frecuencia * (TOP + 1));
        pwm_set_clkdiv(slice, div);
        pwm_set_chan_level(slice, chan, TOP / 2);
        sleep_ms(duracion);
        pwm_set_chan_level(slice, chan, 0);
    }
    sleep_ms(10); // Pequeña pausa entre notas
}

void tocar_silencio(int duracion) {
    sleep_ms(duracion);
} //duración de silencio

void tocar_melodia(uint slice, uint chan) {
    // FA#FA#RESI
    tocar_nota(slice, chan, FAs, CORCHEA);
    tocar_nota(slice, chan, FAs, CORCHEA);
    tocar_nota(slice, chan, RE, CORCHEA);
    tocar_nota(slice, chan, SI, CORCHEA);
    
    // SILENCIO (espacio)
    tocar_silencio(SILENCIO);
    
    // SI
    tocar_nota(slice, chan, SI, CORCHEA);

    tocar_silencio(SILENCIO);

    //MI
    tocar_nota(slice, chan, MI, CORCHEA);
    
    // SILENCIO (espacio)
    tocar_silencio(SILENCIO);
    
    // MI 
    tocar_nota(slice, chan, MI, CORCHEA);
    
    tocar_silencio(SILENCIO);
    
    // MI SOL# LA DO#
    tocar_nota(slice, chan, MI, CORCHEA);
    tocar_nota(slice, chan, SOLs, CORCHEA);
    tocar_nota(slice, chan, SOLs, CORCHEA);
    tocar_nota(slice, chan, LA, CORCHEA);
    tocar_nota(slice, chan, DOs, CORCHEA);
    
    // SILENCIO (espacio)
    tocar_silencio(SILENCIO_MENOR);
    
    // LALA LAMI
    tocar_nota(slice, chan, LA, CORCHEA);
    tocar_nota(slice, chan, LA, CORCHEA);
    tocar_nota(slice, chan, LA, CORCHEA);
    tocar_nota(slice, chan, MI, CORCHEA);

    tocar_silencio(SILENCIO);

    tocar_nota(slice, chan, RE, CORCHEA);
    
    // SILENCIO (espacio)
    tocar_silencio(SILENCIO);
    
    // FA#FA#
    tocar_nota(slice, chan, FAs, CORCHEA);
    
    tocar_silencio(SILENCIO);

    tocar_nota(slice, chan, FAs, CORCHEA);

    tocar_silencio(SILENCIO);

    tocar_nota(slice, chan, FAs, CORCHEA);
    tocar_nota(slice, chan, MI, CORCHEA);
    tocar_nota(slice, chan, MI, CORCHEA);
    tocar_nota(slice, chan, FAs, CORCHEA);
    tocar_nota(slice, chan, MI, CORCHEA);
}

int main() {
    stdio_init_all();

    gpio_set_function(BUZZER_PIN, GPIO_FUNC_PWM);
    uint slice = pwm_gpio_to_slice_num(BUZZER_PIN);
    uint chan  = pwm_gpio_to_channel(BUZZER_PIN);

    pwm_set_wrap(slice, TOP);
    pwm_set_chan_level(slice, chan, TOP / 2);
    pwm_set_enabled(slice, true);

    while (true) {
        tocar_melodia(slice, chan);
        sleep_ms(2000); // Pausa larga antes de repetir
    }
}

```

**Onda cuadrada de PWM a sinusoidal con capacitor**


```bash
// pwm_seno.c — Generar seno 60 Hz con PWM en GPIO 3
#include "pico/stdlib.h"
#include "hardware/pwm.h"
#include <math.h>

#define PIN_PWM    3
#define FREQ_PWM   2000     // 2 kHz portadora
#define TOP        1023     // 10 bits de resolución
#define FREQ_SENO  60       // Señal deseada: 60 Hz

#define PI 3.141592653589793

// Número de muestras por ciclo de seno
#define N_MUESTRAS 100

uint16_t tabla_seno[N_MUESTRAS];

int main() {
    stdio_init_all();

    // --- Generar tabla seno ---
    for (int i = 0; i < N_MUESTRAS; i++) {
        float ang = 2 * PI * i / N_MUESTRAS;
        float val = (sinf(ang) + 1.0f) / 2.0f;  // [0,1]
        tabla_seno[i] = (uint16_t)(val * TOP);
    }

    // --- Configuración PWM ---
    gpio_set_function(PIN_PWM, GPIO_FUNC_PWM);
    uint slice = pwm_gpio_to_slice_num(PIN_PWM);
    uint chan  = pwm_gpio_to_channel(PIN_PWM);

    float f_clk = 125000000.0f; // 125 MHz
    float div = f_clk / (FREQ_PWM * (TOP + 1));
    pwm_set_clkdiv(slice, div);
    pwm_set_wrap(slice, TOP);
    pwm_set_enabled(slice, true);

    // --- Temporización para 60 Hz ---
    // Cada ciclo = 16.67 ms. Dividido en N_MUESTRAS → periodo de actualización:
    float Ts_ms = 1000.0f / (FREQ_SENO * N_MUESTRAS); // ~0.167 ms (167 µs)

    int idx = 0;
    while (true) {
        pwm_set_chan_level(slice, chan, tabla_seno[idx]);
        idx = (idx + 1) % N_MUESTRAS;
        sleep_us((int)(Ts_ms * 1000));
    }
}

```


## 6) Esquemáticos


**Circuito del motor**

| Pico (GPIO) | Pin físico Pico | Conecta a             | Nota                        |
|------------:|:---------------:|----------------------|-----------------------------|
| GP2         | 4               | Puente H → ENA       | PWM para velocidad          |
| GP3         | 5               | Puente H → IN1       | Dirección                   |
| GP4         | 6               | Puente H → IN2       | Dirección                   |
| GP14        | 19              | Botón 1              | Subir velocidad             |
| GP15        | 20              | Botón 2              | Bajar velocidad             |
| GND         | 3               | GND común            | Pico + Puente H + botones   |
| Motor DC    | –               | Puente H → OUT1/OUT2 | Salidas del puente H        |
| VIN externa | –               | Puente H → VIN       | Fuente de motor (no Pico)   |
| Botón 1     | –               | Pull-down → GND      | Estabiliza señal            |
| Botón 2     | –               | Pull-down → GND      | Estabiliza señal            |

**Circuito de buzzer**

<iframe 
  src="https://wokwi.com/projects/443715360633781249?embed" 
  width="800" 
  height="100" 
  allowfullscreen>
</iframe>

**Circuito de señal de PWM sinusoidal**

- _Nota: No existen los capacitores en wokwi, el led que se muestra reemplaza el capacitor. La señal se toma del cable morado hacia el osciloscopio.

<iframe 
  src="https://wokwi.com/projects/443718622783543297" 
  width="800" 
  height="100" 
  allowfullscreen>
</iframe>



## 7) Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/jyWKDoAtEeA" frameborder="0" allowfullscreen></iframe>