# TDSFM: Transmisión Digital Simulada de FM

![Status](https://img.shields.io/badge/Status-Concept%20/%20Prototype-blue)
![License](https://img.shields.io/badge/License-MIT-green)

**TDSFM** (Transmisión Digital Simulada de FM) es un concepto tecnológico y un motor de simulación digital diseñado para replicar de manera exacta el comportamiento, la física de propagación y la degradación analógica de una estación de radio de **Frecuencia Modulada (FM)** tradicional, utilizando como fuente de entrada primaria un flujo de audio digital basado en una **URL de internet**.

A diferencia de los reproductores de streaming web convencionales (que entregan una señal lineal y limpia), el sistema TDSFM altera dinámicamente el audio en función de variables físicas, geográficas y topográficas en tiempo real.

---

## 🚀 Principio de Funcionamiento

El núcleo de TDSFM procesa el flujo de datos proveniente de la URL (Icecast/Shoutcast) y lo somete a un algoritmo de radiofrecuencia (RF) virtual que consta de tres etapas críticas:

1. **Captura del Stream:** El sistema ingesta el audio digital continuo (MP3/AAC).
2. **Procesamiento Analógico Simulado:** Se aplican filtros de preénfasis (50µs/75µs) y compresión multibanda típicos del procesamiento de broadcast de FM.
3. **Inyección de Entorno RF:** Modifica el audio de salida inyectándole estática y reduciendo el ancho de banda dinámicamente según la posición del usuario.

---

## 🛠️ Arquitectura del Sistema

### 1. Integración de Geolocalización (GPS)
El sistema requiere las coordenadas geográficas ($X, Y, Z$) del receptor en tiempo real (por ejemplo, desde el GPS de un smartphone en un coche en movimiento). Al calcular la distancia euclidiana respecto a las coordenadas virtuales de las antenas emisoras, el software determina la atenuación base.

### 2. Designación de Antenas Virtuales
Cada estación de radio dentro del simulador se configura con parámetros técnicos analógicos reales:
* **Coordenadas de emplazamiento:** Latitud y longitud de la torre de transmisión virtual.
* **Altura de la torre ($h_a$):** Crucial para el cálculo del horizonte de radio y la línea de vista.
* **Potencia Radiada Aparente (PRA):** Configurable en vatios (W) o kilovatios (kW) para diferenciar el alcance (ej. una emisora local de 500 W frente a una comercial de 100 kW).

### 3. Modelado de Propagación y Topografía (Montañas)
Para determinar la potencia de la señal recibida ($P_r$), el simulador ejecuta la **Fórmula de Transmisión de Friis** (Pérdidas en el espacio libre):

$$\text{Pérdidas (dB)} = 20 \log_{10}(d) + 20 \log_{10}(f) + 32.44$$

*(Donde $d$ es la distancia calculada por GPS en km y $f$ es la frecuencia de la FM simulada en MHz).*

Además, el sistema cruza los datos con un **Modelo Digital de Terreno (MDT)** en 3D:
* **Línea de vista (Line-of-Sight):** Si no hay obstáculos, la señal decae limpiamente por distancia.
* **Zonas de sombra (Valles/Montañas):** Si una montaña intercepta la línea recta entre la antena y el GPS, el software calcula la pérdida por obstáculos mediante algoritmos de **difracción de filo de cuchillo**, provocando una caída drástica de los decibelios recibidos.

---

## 🔊 Algoritmo de Degradación de Audio y Siseo

La potencia final calculada (en dBm) determina en tiempo real cómo se escuchará la URL mediante un algoritmo de Relación Señal/Ruido (SNR):

* **Fidelidad Máxima (Estéreo Limpio):** Señal óptima (> -65 dBm). El audio de la URL se reproduce al 100% de calidad digital.
* **Conmutación Automática a Mono:** Señal media (-65 a -85 dBm). El sistema reduce la separación estéreo gradualmente hasta hacer el audio monoaural para mitigar el ruido, tal como un autorradio real.
* **Inyección de Ruido Blanco (Siseo):** Señal débil (-85 a -100 dBm). Se mezcla matemáticamente un flujo de ruido estático con el audio de la URL. A menor señal, mayor es el volumen del siseo (*"shhhhh"*).
* **Desvanecimiento (Fading) y Muting:** Señal crítica (< -100 dBm). Se aplican fluctuaciones aleatorias por trayectos múltiples. Al perderse la señal por completo tras una montaña, la URL se silencia y solo se emite estática pura.

---

## 🎯 Aplicaciones Principales

* **Videojuegos y Simuladores de Conducción:** Integración de radios locales realistas que ganan o pierden señal dinámicamente según el mapa del juego.
* **Entornos de Prueba de Laboratorio:** Testeo de receptores físicos simulando condiciones reales sin necesidad de emitir ondas reales al espacio radioeléctrico (evitando problemas de licencias).
* **Proyectos de Nostalgia y Preservación Digital:** Plataformas web interactivas que replican la experiencia física del dial analógico usando flujos modernos de internet.

---

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Consulta el archivo `LICENSE` para más detalles.
