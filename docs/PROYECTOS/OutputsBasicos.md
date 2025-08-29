# 📚 Ejemplo de Documentación del Proyecto

> Plantilla genérica para documentar proyectos académicos o de ingeniería.  
> Copia y adapta las secciones según tu necesidad.

---

## 1) Resumen

- **Nombre del proyecto:** _Outputs básicos_  
- **Equipo / Autor(es):** Juan David García Cortéz, Sumie Arai Erazzo, Carlos Gutierrez Martinez, Máximo Nájera Medina _  
- **Curso / Asignatura:** _Sistemas Embebidos 1_  
- **Fecha:** _08/27/2025_  
- **Descripción breve:** _Se realizó un conjunto de codigo para raspberry pico 1 con las librerias "pico/stdlib.h" y "hardware/structs/sio.h"._

---

## 2) Objetivos

- **General:** _Aprender a usar mascaras y comandos de las llibrerias vistas _
- **Específicos:**
  - _Crear un contador de 4 bits_
  - _Correr un “1” por cinco LEDs P0..P3 y regresar (0→1→2→3→2→1)_
  - _Secuencia en codigo Gray_

## 3) Alcance y Exclusiones

- **Incluye:** _Qué funcionalidades/entregables sí están en el proyecto._
- **No incluye:** _Qué queda fuera para evitar malentendidos._

---

## 4) Requisitos

**Software**
- _SO compatible (Windows/Linux/macOS)_
- _Python 3.x / Node 18+ / Arduino IDE / etc._
- _Dependencias (p. ej., pip/requirements, npm packages)_

**Hardware (si aplica)**
- _MCU / Sensores / Actuadores / Fuente de poder_
- _Herramientas (multímetro, cautín, etc.)_

**Conocimientos previos**
- _Programación básica en X_
- _Electrónica básica_
- _Git/GitHub_

---

## 5) Instalación

```bash
# 1) Clonar
git clone https://github.com/<usuario>/<repo>.git
cd <repo>

# 2) (Opcional) Crear entorno virtual
python -m venv .venv
# macOS/Linux
source .venv/bin/activate
# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# 3) Instalar dependencias (ejemplos)
pip install -r requirements.txt
# o, si es Node:
npm install


```