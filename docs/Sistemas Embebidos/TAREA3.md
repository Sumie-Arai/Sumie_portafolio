# 📚 TAREA 3

---

## 1) Resumen

- **Nombre del proyecto:** _Inputs_  
- **Equipo / Autor(es):** _Juan David García Cortéz y Sumie Arai Erazo_  
- **Curso / Asignatura:** _Sistemas embebidos 1_  
- **Fecha:** _7/31/25_  
- **Descripción breve:** _En esta practica se utilizan máscaras para recrear compuertas lógicas en pi pico con C SDK, con dos inputs con resistencias pull-up. Además de un código que controla 5 LEDs que se prenden uno por uno en orden y avanza la secuencia o retrocede cada que se pica un boton de avance u otro de retroceso._


## 2) Objetivos

- **General:** _Crear codigos que contengan imputs y outputs_
- **Específicos:**
  - _Aprender a usar resistencias pull-up_
  - _Simular compuertas lógicas con máscaras_
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

### Compuerta AND, OR y XOR

```bash
#include "pico/stdlib.h" // Para usar las funciones de GPIO
// #include "hardware/gpio.h"        // Para usar las funciones de GPIO

#define BotonX 4
#define BotonY 5
#define LedX 6
#define LedY 7
#define LedZ_AND 8
#define LedZ_OR 9
#define LedZ_XOR 10

int main()
{
    const uint32_t Mascara = (1u << BotonX | 1u << BotonY | 1u << LedX | 1u << LedY | 1u << LedZ_AND | 1u << LedZ_OR | 1u << LedZ_XOR);

    gpio_init_mask(Mascara);                                                                                                                 // Inicializa los pines
    gpio_set_dir_masked(Mascara, (0u << BotonX | 0u << BotonY | 1u << LedX | 1u << LedY | 1u << LedZ_AND | 1u << LedZ_OR | 1u << LedZ_XOR)); // Configura los pines como salida
    gpio_pull_up(BotonX);                                                                                                                    // Activa la resistencia pull-up interna del pin 4
    gpio_pull_up(BotonY);

    while (true)
    {
        int Entrada_X = !gpio_get(BotonX);
        int Entrada_Y = !gpio_get(BotonY);
        int Salida_AND, Salida_OR, Salida_XOR;

        Salida_AND = Entrada_X & Entrada_Y; // Pin 8 = Pin 4 AND Pin 5
        Salida_OR = Entrada_X | Entrada_Y;  // Pin 9 = Pin 4 OR Pin 5
        Salida_XOR = Entrada_X ^ Entrada_Y; // Pin 10 = Pin 4 XOR Pin 5

        gpio_put_masked(Mascara, (1 << 11) | (Entrada_X << LedX) | (Entrada_Y << LedY) | (Salida_AND << LedZ_AND) | (Salida_OR << LedZ_OR) | (Salida_XOR << LedZ_XOR));
        sleep_ms(100); // Pausa de 100 ms
    }
}

```

### Secuencia LEDs

```bash
#include "pico/stdlib.h" // Para usar las funciones de GPIO
// #include "hardware/gpio.h"        // Para usar las funciones de GPIO

#define BotonX 4
#define BotonY 5
#define LedX 6
#define LedY 7
#define LedZ_AND 8
#define LedZ_OR 9
#define LedZ_XOR 10

int main()
{
    const uint32_t Mascara = (1u << BotonX | 1u << BotonY | 1u << LedX | 1u << LedY | 1u << LedZ_AND | 1u << LedZ_OR | 1u << LedZ_XOR);

    gpio_init_mask(Mascara);                                                                                                                 // Inicializa los pines
    gpio_set_dir_masked(Mascara, (0u << BotonX | 0u << BotonY | 1u << LedX | 1u << LedY | 1u << LedZ_AND | 1u << LedZ_OR | 1u << LedZ_XOR)); // Configura los pines como salida
    gpio_pull_up(BotonX);                                                                                                                    // Activa la resistencia pull-up interna del pin 4
    gpio_pull_up(BotonY);

    int posicion = 6;
    bool presionado = false;

    while (true)
    {
        int Boton_Izq = !gpio_get(BotonX);
        int Boton_Der = !gpio_get(BotonY);

        if (Boton_Izq == 1 && presionado == false)
        {
            presionado = true;
            if (posicion == 6)
                posicion = 11;
            posicion--;
        }
        if (Boton_Der == 1 && presionado == false)
        {
            presionado = true;
            if (posicion == 10)
                posicion = 5;
            posicion++;
        }
        if (Boton_Izq == 0 && Boton_Der == 0)
            presionado = false;
        gpio_put_masked(Mascara, (1u << posicion)); // Enciende el LED en la posición actual
        sleep_ms(10);                              // Pausa de 100 ms
    }
}

```



## 6) Esquemático

![esquematico 1](recursos/imgs/ESQtarea3.png)

<!-- Control de tamaño usando HTML -->
<img src="recursos/imgs/ESQtarea3.png" alt="Diagrama del sistema" width="420">



## 7) Video

### Compuertas AND, OR y XOR
<iframe width="560" height="315" 
        src="https://www.youtube.com/embed/qnn4jXtUNFQ" 
        title="Compuertas AND, OR y XOR" 
        frameborder="0" 
        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
        allowfullscreen>
</iframe>

### Secuencia de LEDs adelante y atrás
<iframe width="560" height="315" 
        src="https://www.youtube.com/embed/5GKeFAy4P7I" 
        title="Secuencia de LEDs" 
        frameborder="0" 
        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
        allowfullscreen>
</iframe>



