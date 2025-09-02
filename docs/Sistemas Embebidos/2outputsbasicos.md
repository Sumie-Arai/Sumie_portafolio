# 📚 TAREA 3

---

## 1) Resumen

- **Nombre del proyecto:** _Outputs Básicos_  
- **Equipo / Autor(es):** _Juan David García Cortéz y Sumie Arai Erazo_  
- **Curso / Asignatura:** _Sistemas embebidos 1_  
- **Fecha:** _7/31/25_  
- **Descripción breve:** _En esta practica se formuñó un código simple que mande una señal de high para prender 5 LEDs uno por uno de forma que se vea un patrón de rebote de encendido en la tira de LEDs._



## 2) Objetivos

- **General:** _Crear codigos que contengan imputs y outputs_
- **Específicos:**
  - _Aprender a prender pines de pi pico con C SDK_
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

```bash
#include "pico/stdlib.h" // Para usar las funciones de GPIO
#include "hardware/gpio.h"        // Para usar las funciones de GPIO


#define P1 4 // BOTÓN de Jugador 1
#define P2 5 // BOTÓN de Jugador 2
#define Led1 6
#define Led2 7
#define Led3 8
#define Led4 9
#define Led5 10


int main()
{
    const uint32_t Mascara = (1u << Led1 | 1u << Led2 | 1u << Led3 | 1u << Led4 | 1u << Led5);

    gpio_init_mask(Mascara);                                                                                                                 // Inicializa los pines
    gpio_set_dir_masked(Mascara, (1u << Led1 | 1u << Led2 | 1u << Led3 | 1u << Led4 | 1u << Led5)); // Configura los pines como salida
    

    int posicion = 6;

    int direccion = 1; // 1 para derecha, -1 para izquierda

    while (true)
    {
        gpio_put_masked(Mascara, (1u << posicion)); // Enciende el LED en la posición actual
        sleep_ms(200);                             // Pausa de 200 ms

        posicion += direccion; // Actualiza la posición
        if (posicion == Led5) direccion = -1;
        else if (posicion == Led1) direccion = 1;

    }
}


```



## 6) Esquemático


<img src="recursos\imgs\ESQtarea3.jpg" alt="esquematico 1" width="420">


## 7) Video


<!-- Pega esto en tu .md -->
<iframe
  width="315"
  height="560"
  src="https://www.youtube-nocookie.com/embed/daY4RG9xRPI?rel=0&modestbranding=1"
  title="YouTube video player"
  frameborder="0"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
  allowfullscreen>
</iframe>
