# 📻 TDSFM: Transmisión Digital Simulada de FM

![GitHub release (latest by date)](https://img.shields.io/github/v/release/trsnetspain/infortaiment/?color=blue&logo=github)
![Build Status](https://img.shields.io/badge/build-passing-brightgreen?style=flat-square&logo=github-actions)
![Python Version](https://img.shields.io/badge/python-3.8%20%7C%203.9%20%7C%203.10-blue?style=flat-square&logo=python)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)

**TDSFM** (Transmisión Digital Simulada de FM) es un motor de simulación digital en tiempo real que clona la física de propagación, la degradación de RF y el comportamiento acústico de una emisora de radio de **Frecuencia Modulada (FM)** analógica, utilizando como fuente de entrada primaria un flujo de audio digital continuo extraído de una **URL** (Icecast/Shoutcast).

A diferencia del streaming convencional que ofrece una recepción lineal y perfecta, TDSFM inyecta imperfecciones físicas (siseo, atenuación, pérdida de estéreo, zonas de sombra por montañas) basándose dinámicamente en la telemetría GPS del usuario.

---

## 🧭 Tabla de Contenidos
* [🚀 Demostración Visual de Estados](#-demostración-visual-de-estados)
* [🧠 Arquitectura del Sistema](#-arquitectura-del-sistema)
* [📈 Modelado de Radiofrecuencia (RF)](#-modelado-de-radiofrecuencia-rf)
* [💻 Código Fuente del Simulador (main.py)](#-código-fuente-del-simulador-mainpy)
* [📊 Comparativa: Streaming vs TDSFM](#-comparativa-streaming-vs-tdsfm)
* [📄 Licencia](#-licencia)

---

## 🚀 Demostración Visual de Estados

El procesador dinámico analiza la potencia de la señal recibida ($P_r$) medida en **dBm** y altera el flujo de la URL en tiempo real a través de cuatro estados discretos:

| Potencia (dBm) | Estado del Receptor | Representación del Audio de Salida | Efecto Acústico |
| :--- | :--- | :--- | :--- |
| **> -65 dBm** | 🟢 Estéreo Limpio | `████████████████████` | Audio digital HD al 100% de fidelidad. |
| **-65 a -85 dBm** | 🟡 Mono Automático | `███████████████░░░░░` | Mezcla progresiva a mono para mitigar ruido. |
| **-85 a -100 dBm**| 🟠 Señal Débil | `██████░░░░░░░░░░░░░░` | Siseo analógico (*ruido blanco*) predominante. |
| **< -100 dBm** | 🔴 Estática Absoluta | `░░░░░░░░░░░░░░░░░░░░` | *Muting* de la URL. Solo ruido estático puro. |

---

## 🧠 Arquitectura del Sistema

El ecosistema TDSFM se compone de tres módulos core interconectados:
1. **Ingesta de datos (GPS):** Captura vectores de movimiento del dispositivo receptor a frecuencias de refresco variables (ej. 10Hz para simulación vehicular).
2. **Designación de Antenas:** Soporta la inyección de múltiples antenas virtuales concurrentes configuradas con coordenadas geográficas, altura de torre ($h_a$) y Potencia Radiada Aparente (PRA) independiente.
3. **Filtros de Radiofrecuencia:** Simulación activa de filtros de preénfasis (50 µs / 75 µs), atenuación por interferencia de canal adyacente y límites de saturación del limitador del receptor.

---

## 📈 Modelado de Radiofrecuencia (RF)

### Pérdidas en el Espacio Libre
Para calcular la degradación matemática de la onda por distancia pura, el motor implementa el **Modelo de propagación en espacio libre (Ecuación de Friis)**:

$$\text{Pérdidas (dB)} = 20 \log_{10}(d) + 20 \log_{10}(f) + 32.44$$

Donde $d$ representa la distancia euclidiana calculada por GPS en kilómetros y $f$ es la frecuencia asignada a la estación de FM en megahercios (MHz).

### Análisis Topográfico (Zonas de Sombra)
El software proyecta en tiempo real una línea de vista (*Line-of-Sight*) entre la antena y el GPS a través de un **Modelo Digital de Terreno (MDT)**. Si un obstáculo geográfico (como una montaña) intercepta el haz virtual, el procesador calcula las pérdidas por inserción mediante el algoritmo de **difracción de filo de cuchillo**, simulando con precisión matemática las "zonas débiles" o valles sin señal.

---
