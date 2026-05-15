# Monitoreo Geomecánico y Emisiones de Metano en Vertederos mediante Teledetección 🛰️🌍

Este repositorio contiene el código y la metodología desarrollada durante las prácticas externas en el Instituto de Productos Naturales y Agrobiología (IPNA-CSIC) y la Universidad de La Laguna. 

El proyecto evalúa conjuntamente la evolución geomecánica (subsidencia) y las emisiones puntuales de metano ($CH_4$) en complejos ambientales de gestión de residuos, centrándose en el vertedero de Arico (Tenerife) y Valdemingómez (Madrid).

## Objetivos del Proyecto
1. **Análisis de deformación del terreno:** Evaluación de la compactación de los vertederos utilizando datos interferométricos (InSAR) del *European Ground Motion Service* (EGMS).
2. **Detección de emisiones:** Identificación de anomalías de $CH_4$ atmosférico utilizando el instrumento TROPOMI a bordo del satélite Sentinel-5P.
3. **Cuantificación de flujos:** Estimación de las tasas de emisión de biogás aplicando el método de *Integrated Mass Enhancement* (IME) apoyado por reanálisis meteorológico (ERA5).

## 📂 Estructura del Repositorio

* `/notebooks/`: Contiene los Jupyter Notebooks con el flujo de trabajo principal.
  * `01_descarga_datos_TROPOMI_ERA5.ipynb`: Scripts para la consulta, descarga y filtrado de datos L2 de TROPOMI y viento de ERA5.
  * `02_calculo_subsidencia_InSAR.ipynb`: Descomposición de datos InSAR de alta resolución (L2B) en velocidades vertical y horizontal (este-oeste).
  * `03_cuantificacion_IME_Madrid.ipynb`: Aislamiento de plumas, análisis del fondo atmosférico y aplicación del método IME para el vertedero de Valdemingómez.
  * `04_analisis_Tenerife.ipynb`: Análisis de dispersión y evaluación de fuentes en el Complejo Ambiental de Arico bajo diferentes regímenes de viento (Alisios vs SW).
* `/data/`: (No subida al repositorio por peso). Estructura para almacenar los archivos NetCDF y Excel generados.
* `/docs/`: Incluye la memoria completa del proyecto en PDF con los resultados, discusión técnica y mapas generados.

## Tecnologías y Datos Utilizados
* **Lenguajes y Entornos:** Python, Jupyter Notebook, Google Colab.
* **Procesamiento Geoespacial:** `geopandas`, `pyproj`, Microsoft Planetary Computer.
* **Fuentes de Datos:**
  * **Copernicus EGMS (Sentinel-1):** Productos L2B (Ascendente y Descendente).
  * **Sentinel-5P (TROPOMI):** Datos L2 de metano *bias corrected*.
  * **ECMWF ERA5:** Componentes del viento a 10 metros ($u_{10}$ y $v_{10}$).

## Resultados Destacados
* Desarrollo de un algoritmo que proyecta el movimiento diagonal de la línea de visión (LOS) del radar para detectar hundimientos extremos de hasta -102.9 mm/año en celdas antiguas de Arico.
* Implementación exitosa del método IME en Valdemingómez, estimando una tasa de emisión de 9.6 t/h.
* Análisis crítico de la influencia orográfica sobre las detecciones de TROPOMI en Canarias (efecto de la rugosidad del terreno).
* Evaluación de plumas de metano en Arico (Tenerife), demostrando que la ausencia de correlación entre la acumulación de metano en la atmósfera y el régimen de vientos sugiere la existencia de múltiples fuentes de emisión difusas en el sur de la isla, en lugar de un único foco centralizado en el vertedero.

---
*Autoría: Alize Michelena Ferrando*
