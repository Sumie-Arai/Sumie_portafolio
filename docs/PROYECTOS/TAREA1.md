# 📚 TAREA 1

> Plantilla genérica para documentar proyectos académicos o de ingeniería.  
> Copia y adapta las secciones según tu necesidad.

---

## 1) Resumen

- **Nombre del proyecto:** _Comparación de microcontroladores_  
- **Equipo / Autor(es):** _Sumie Arai_  
- **Curso / Asignatura:** _Sistemas Embebidos 1_  
- **Fecha:** _25/06/2025_  
- **Descripción breve:** _Se realiza una breve investigación y comparación de 4 microcontroladores que se planean utilizar para un proyecto hipotético en el que se construirá un robot autómata que sepa bailar vals._  

!!! tip "Consejo"
    Mantén este resumen corto (máx. 5 líneas). Lo demás va en secciones específicas.

---

## 2) Objetivos

- **General:** _Tener un conocimiento más amplio sobre los distintos microcontroladores y sus características individuales y ventajas para futuros proyectos._  
- **Específicos:**
  - _Familiarizarse con las herramientas gratuitas que GitHub ofrece, como esta página._
  - _Adquirir un mejor entendimiento sobre los componentes de un microcontrolador._
  
---

# MICROCONTROLADORES

---

## 1) Arduino Mega (ATmega2560)

- **Pines / Periféricos:** _54 GPIO, 16 entradas analógicas, UART, SPI, I²C_  
- **Memoria:** _256 KB Flash, 8 KB SRAM_  
- **Ecosistema / Lenguaje:** _Arduino IDE (C/C++)_  
- **Costos:** _$20-30 USD aprox._  
- **Arquitectura:** _AVR 8-bit_  
- **Velocidad:** _16 MHz_  
- **Descripción breve:** _Muchos pines para servos o sensores. Muy usado en robótica educativa, fácil de programar, pero limitado en velocidad._  

---

## 2) ESP32

- **Pines / Periféricos:** _34 GPIO, ADC, PWM, UART, SPI, I²C, Wi-Fi, BT_  
- **Memoria:** _4 MB Flash, 520 KB SRAM_  
- **Ecosistema / Lenguaje:** _Arduino IDE, ESP-IDF, MicroPython_  
- **Costos:** _$5-10 USD aprox._  
- **Arquitectura:** _Xtensa LX6 (32-bit)_  
- **Velocidad:** _Dual-core 240 MHz_  
- **Descripción breve:** _Alta velocidad y conectividad. Permite sincronizar baile con música vía Bluetooth o Wi-Fi. Menos pines que el Mega, pero muy potente. Puede programarse en Arduino o Python, lo que da facilidad de uso._  

---

## 3) STM32F4 (Cortex-M4)

- **Pines / Periféricos:** _Varía: ~50+ GPIO, ADC, DAC, timers, CAN, SPI, I²C, UART_  
- **Memoria:** _1 MB Flash, 192 KB RAM_  
- **Ecosistema / Lenguaje:** _STM32CubeIDE, Arduino core, C/C++_  
- **Costos:** _$15-30 USD aprox._  
- **Arquitectura:** _ARM Cortex-M4_  
- **Velocidad:** _Hasta 180 MHz_  
- **Descripción breve:** _Potente y con hardware para control en tiempo real. Muy usado en robótica profesional, aunque con mayor curva de aprendizaje._  

---

## 4) Teensy 4.1 (Cortex-M7)

- **Pines / Periféricos:** _~55 GPIO, PWM, CAN, USB host, SPI, I²C, UART_  
- **Memoria:** _8 MB Flash, 1 MB RAM_  
- **Ecosistema / Lenguaje:** _Arduino IDE, C/C++_  
- **Costos:** _$30-40 USD aprox._  
- **Arquitectura:** _ARM Cortex-M7_  
- **Velocidad:** _600 MHz_  
- **Descripción breve:** _Altísima velocidad y memoria → excelente para rutinas complejas de movimiento y audio. Buena integración con Arduino._  

---

## Conclusiones

- **STM32F4:** _Es bastante utilizado para robótica y control en tiempo real. Sus timers facilitan el control de servos. Su FPU es útil para calcular trayectorias._  
- **ESP32:** _Muy balanceado en costo y prestaciones, ideal para proyectos con conectividad._  
- **Teensy 4.1:** _El más rápido, pero más caro y menos difundido._  
- **Arduino Mega:** _Sencillo y versátil para proyectos educativos, pero limitado en potencia._  
