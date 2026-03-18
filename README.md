# Emisiones de metano del vertedero de Arico (Teledetección)
Proyecto ULL/IPNA-CSIC enfocado en comparar la evolución de las emisiones de metano (CH4) y la compactación del terreno en el vertedero de Arico (2020-2025). Empleando teledetección (TROPOMI, EMIT) y datos EGMS para calcular flujos y analizar su correlación usando Python y QGIS.
# Día 1

## Área de Interés (AOI)
He localizado el Complejo Ambiental de Tenerife (CAT-Arico) y estas son las coordenadas que he obtenido:

- **Latitud:** [28.11769]
- **Longitud:** [16.48005]
- **Ubicación:** Arico, Tenerife.

Para tomar el área de aproximadamente un radio de 10-30 km al rededor de nuestras coordenadas, vamos a tomar un cuadrado sumandole y restandole 22 km en la la latitud y en la longitud. Teniendo en cuenta que 0.1º son unos 11km, si le sumamos y restamos 0.2º, obtenemos un cuadrado de 44km de alto y 44km de ancho. Por lo que, tendremos:

- **Latitud Norte (Lat + 0.2º)**: [28.31769]
- **Latitud Sur (Lat - 0.2º)**: [27.91769]
- **Longitud Este (Long + 0.2º)**: [16.68005]
- **Longitud Oeste (Long - 0.2º)**: [16.28005]

Lectura de documentación:

## TROPOMI (Medición gases atmósfera)

-**TROPOMI**: TROPOspheric monitoring instrument, es el instrumento (sensor o cámara) que va a borde del sentinel-5p de la ESA. Es un espectrómetro que mide gases en la atmósfera (por ejemplo el métano). Tiene una alta resolución, por eso nos permitirá detectar fuentes de emisión de vertederos. 

-**PUM**: Product User Manual, es el manual de instrucciones de los archivos que contienen la información. Nos indica que carpetas hay en el archivo, como filtrar datos malos (qa_value), nos indica en que unidades vien ele metano...

Entonces, sobre TROPOMI, según el PUM, se deben monitorizar los parámetros de Quality Assurance. Para el análisi de metano, el umbral recomendado para el filtrado de datos suele ser qa_value > 0.5. 

En cuanto al Average Kernel: el satelite captura mejor la información de la estratosfera, y peor la atmosfera cercana al suelo. El Average Kernel indica cuanta sensibilidad tiene el satélite en cada capa de la atmósfera según la altura. 
- **Kernel 1**: el satelite ve perfectamente lo que ocurre en la capa.
- **Kernel 0**: no ve nada

También es importante hablar de los niveles. Estos indican como de procesado está el dato que toma el satélite.

- **Nivel 0 (L0)**: Dato crudo.
- **Nivel 1 (L1)**: Es el dato geolocalizado y calibrado. La luz se convierte a unidades de energía. L1B te indica cuanta luz de cada color llegó al sensor.
- **Nivel 2 (L2)**: Es el dato geofísico. Un software analiza la luz de L1 y calcula la cantidad de moléculas que hay de un gas específicio. Caso TROPOMI L2 CH_4, nos da directamente la concentración de metano en partes por mil millones (ppb) para cada pixel de mi AOI. Esto es loq ue descargaremos y analizaremos en python (?).
- **Nivel 3 (L3)**: Dato mapeado y promediado. L2 son tiras de datos que tomó ese día el satlélite. L3 coge los datos de varios días y se oclocan en un mapa limpio. Se pueden perder fugas de metano.

## Monitoreo de Movimiento del Suelo (EGMS)

El **European Ground Motion Service (EGMS)** es un servicio de Copernicus que mide cuánto cambia la altitud el suelo con precisión milimétrica.

### 1. Objetivo
Detectar posibles procesos de **subsidencia** (hundimiento) en las celdas del vertedero que puedan estar relacionados con la emisión de metano o la compactación de residuos.

### 2. Productos a utilizar (Disponibles desde 2016)
* **Basic (L2a):** Movimiento medido en la línea de visión del satélite (LOS) respecto a un punto de referencia local.
* **Calibrated (L2b):** Movimiento ajustado con modelos globales GNSS, permitiendo mediciones absolutas ya que compara el movimiento de Arico con el de cualquier otra parte de Europa.
* **Ortho (L3):** Datos procesados para mostrar únicamente el movimiento vertical (arriba/abajo) u horizontal, facilitando la interpretación directa del hundimiento.
* 
### 3. Parámetro clave
- **Velocidad de deformación:** Medida en milímetros por año (mm/y) para identificar cambios estructurales en el terreno a largo plazo.

## Identificación de Plumas con EMIT

EMIT (Earth Surface Mineral Dust Source Investigation) es un instrumento de la NASA que está instalado en la Estación Espacial Internacional (ISS). Aunque su misión principal es estudiar el polvo mineral, se ha convertido en una herramienta para detectar metano.

- **Instrumento:** Espectrómetro de imágenes instalado en la Estación Espacial Internacional.
- **Resolución:** 60 metros (Alta resolución), ideal para identificar "puntos calientes" o plumas individuales en el complejo de Arico.
- **Producto L2B Plume Complexes:** Archivos que muestran la concentración de metano por encima del nivel de fondo, permitiendo visualizar la forma y dirección de la pluma de gas.
- **Uso en el proyecto:** Se utilizará para validar si las detecciones de TROPOMI provienen de focos específicos dentro del vertedero.


¿Qué capas tomamos de EMGS?
**Calibrated L2b ascendente**: A08-162, A07-060
**Calibrated L2b descendent**: D11-096

En python hice un mix para sacar el desplazamiento vertical, y comparar los resultados con el desplazamiento vertical que vien een los datos Ortho l3, pero estos tinene menor resolución porque hacen un amedia con muchos datos obtenidos en una zona.


## Metodología de Procesamiento InSAR (EGMS) para el Complejo Ambiental de Arico

### 1. Objetivo
El objetivo de esta fase del proyecto es cuantificar la subsidencia (hundimiento del terreno) en el Complejo Ambiental de Tenerife (Arico) utilizando datos de radar de apertura sintética (InSAR) provenientes del European Ground Motion Service (EGMS). 

Dado que los satélites Sentinel-1 miden el desplazamiento en una trayectoria diagonal (Línea de Visión o *Line of Sight* - LOS), es necesario combinar mediciones desde dos perspectivas orbitales distintas para obtener el movimiento vertical real del terreno.

### 2. Adquisición y Preparación de Datos
Se descargaron los productos de Nivel 2B (Calibrated) (calibrados respecto a un punto de referencia terrestre con gps) del EGMS para el periodo 2019-2023, que contienen la velocidad media anual y los ángulos de observación precisos para cada Punto de Medición (MP).
* **Órbita Ascendente:** El satélite viaja de Sur a Norte, observando hacia el Este.
* **Órbita Descendente:** El satélite viaja de Norte a Sur, observando hacia el Oeste.

Para combinar ambos conjuntos de datos, se realizó un cruce espacial (*spatial merge*). Las coordenadas de latitud y longitud se redondearon a 4 cifras decimales (aproximadamente 11 metros de precisión en el ecuador), lo que permitió identificar qué puntos del terreno fueron observados de manera efectiva por ambas órbitas.

### 3. Descomposición Geométrica 2D (Ecuaciones)
El desplazamiento medido por el satélite en su Línea de Visión ($V_{LOS}$) es una proyección del vector de movimiento real en 3D (Vertical, Este-Oeste y Norte-Sur). Debido a la órbita casi polar del satélite Sentinel-1, la sensibilidad al movimiento Norte-Sur es mínima y se asume como cero en este cálculo bidimensional.

La relación matemática entre la velocidad observada y los vectores de movimiento real se define por la matriz de proyección:

$$\begin{pmatrix} V_{asc} \\ V_{desc} \end{pmatrix} = \begin{pmatrix} \cos(\theta_{asc}) & -\sin(\theta_{asc})\cos(\alpha_{asc}) \\ \cos(\theta_{desc}) & -\sin(\theta_{desc})\cos(\alpha_{desc}) \end{pmatrix} \begin{pmatrix} V_U \\ V_E \end{pmatrix}$$

Donde:
* $V_{asc}$ y $V_{desc}$ son las velocidades medias medidas en el LOS (mm/año).
* $\theta$ es el ángulo de incidencia (`incidence_angle`).
* $\alpha$ es el ángulo de azimut de la órbita (`track_angle`).
* $V_U$ es la velocidad de desplazamiento Vertical.
* $V_E$ es la velocidad de desplazamiento Este-Oeste.

Para resolver este sistema de ecuaciones y despejar las incógnitas ($V_U$ y $V_E$) de los cientos de miles de puntos de manera computacionalmente eficiente, definimos las constantes trigonométricas:
* $A = \cos(\theta)$
* $B = -\sin(\theta)\cos(\alpha)$

Aplicando la regla de Cramer para resolver el sistema lineal de $2 \times 2$, el cálculo directo aplicado en el código de Python es:

**Cálculo de la Velocidad Vertical ($V_U$):**
$$V_U = \frac{V_{asc} B_{desc} - V_{desc} B_{asc}}{A_{asc} B_{desc} - A_{desc} B_{asc}}$$

**Cálculo de la Velocidad Este-Oeste ($V_E$):**
$$V_E = \frac{A_{asc} V_{desc} - A_{desc} V_{asc}}{A_{asc} B_{desc} - A_{desc} B_{asc}}$$

*(Nota: Un valor negativo en $V_U$ indica subsidencia o hundimiento del terreno).*

### 4. Resultados y Visualización
Tras procesar más de 800.000 puntos cruzados, se identificó que el terreno natural circundante presenta una gran estabilidad (media cercana a -1.1 mm/año). Sin embargo, al centrar el área de estudio en las coordenadas del vertedero, se aislaron puntos con una subsidencia extrema, alcanzando valores de hasta **-102.9 mm/año**. 

Los datos resultantes se exportaron y se mapearon utilizando una paleta de colores divergente (Rojo para hundimiento, Verde para estabilidad), revelando de forma nítida la huella de compactación de las celdas de residuos sólidos en el Complejo Ambiental de Arico.

Lo primero q voy a descargar es: ascendente: l2b 060 2019-2023 y descendente l2b096 2019-2023

