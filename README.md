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

-**TROPOMI**: TROPOspheric monitoring instrument, es un sensor espectrométrico a bordo del sentinel-5p de la ESA, mide gases de efecto invernadero en la atmósfera (por ejemplo el métano). 
Su resolución espacial relativamente alta (~7 km × 7 km) permite detectar anomalías regionales de metano, aunque no plumas puntuales muy pequeñas.

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

# Día 2

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

## EMGS

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

# Día 3: Refinamiento de Resolución y Análisis Vectorial Horizontal (InSAR)

En esta jornada, el análisis de la deformación del Complejo Ambiental de Arico ha dado un salto cualitativo. Hemos pasado de una visualización unidimensional (hundimiento) a un estudio bidimensional (Vertical + Horizontal) con calibración de errores satelitales.

---
## Python
### 1. Ajuste de Resolución a la Rejilla de 20 Metros
El primer paso del día consistió en reajustar la cuadrícula de interpolación de los datos InSAR. 
* **El problema:** Inicialmente se usó una resolución de 11 metros, lo cual generaba demasiado "ruido" y artefactos matemáticos al forzar una resolución superior a la capacidad real del satélite.
* **La solución:** Se recalculó el cruce de las órbitas Ascendente y Descendente redondeando las coordenadas a pasos de **20 metros** (aprox. `0.00018` grados en latitud y `0.00020` en longitud). Esto coincide de forma mucho más precisa con la resolución espacial azimutal nativa del radar Sentinel-1, obteniendo una nube de puntos más robusta y fiable.

---

### 2. Dinámicas Geomecánicas del Vertedero: Teoría Acoplada
Antes de calcular los vectores horizontales, analizamos el comportamiento físico de una celda de vertido para poder interpretar correctamente los resultados. La deformación de un vertedero no es unidireccional, sino que obedece a un acoplamiento mecánico:

1. **Subsidencia Vertical (El Motor):** Impulsada por la compactación de las cargas y, sobre todo, por la biodegradación de la materia orgánica (pérdida de masa al transformarse en gas y lixiviados).
2. **Expansión Lateral o *Lateral Spreading* (La Consecuencia):** Al sufrir esta enorme presión vertical, la masa de residuos (que se comporta como un material elástico-plástico) busca liberar tensión desplazándose hacia los laterales (hacia los taludes del vaso).



---

### 3. Extracción de la Componente Horizontal (Este-Oeste)
Utilizando la geometría de visión lateral (Side-looking) del radar y trigonometría básica, se resolvió el sistema de ecuaciones para combinar la *Mean Velocity* de la órbita Ascendente y Descendente. 



* **Limitación del satélite:** Debido a la órbita cuasi-polar del Sentinel-1, el sistema es prácticamente "ciego" a los movimientos Norte-Sur. Por lo tanto, el cálculo se limitó a obtener un vector 2D preciso: la velocidad **Vertical (Up-Down)** y la velocidad **Horizontal (East-West)**.

---

### 4. Identificación y Corrección del Sesgo de Referencia (Detrending)
Al obtener los primeros resultados horizontales, se detectó una anomalía: *toda la isla* parecía desplazarse uniformemente hacia el Oeste a unos **-8 mm/año**. 

* **Diagnóstico:** Se identificó este fenómeno como un **Sesgo del Punto de Referencia (Reference Point Bias)**. En la técnica InSAR, el movimiento es relativo a un píxel considerado "estable". Si dicho punto sufre dinámicas regionales (como deslizamientos de ladera a gran escala o hundimiento por el peso de la isla), transfiere ese error al resto del mapa.
* **Calibración:** Se aplicó una técnica de *Detrending*. Filtramos las zonas verdaderamente estables (donde el hundimiento vertical era casi nulo, entre -2 y +2 mm/año), calculamos su velocidad horizontal promedio (el error de *offset*) y se lo restamos a todo el mapa. Esto permitió centrar los valores reales y aislar el movimiento puramente local del vertedero.

---

### 5. Visualización Vectorial Avanzada
Para finalizar, se generó un mapa de vectores para interpretar la deformación de forma intuitiva:
* **Fondo:** Se mantuvo el mapa de hundimiento vertical (escala Rojo-Verde) con baja opacidad para dar contexto.
* **Vectores (Flechas):** Se filtró el ruido para mostrar únicamente los desplazamientos críticos (superiores a **±10 mm/año**). Para evitar la saturación visual, se estandarizó la longitud de todas las flechas (indicando solo la dirección) y se aplicó una **escala de colores personalizada** (Violeta-Rosa para la expansión Oeste; Azul claro-oscuro para la expansión Este) para representar la magnitud de la fuerza.

**Conclusión:** El mapa vectorial calibrado confirma visualmente la hipótesis de *Lateral Spreading*. El Vaso 3 del Complejo Ambiental se está hundiendo a tasas de $\approx 100$ mm/año y, como consecuencia directa, la masa de residuos está "empujando" hacia los bordes Este y Oeste a velocidades de hasta **45 mm/año**, lo que supone un dato crítico para la monitorización de la estabilidad de los taludes y la integridad de las geomembranas.

## GrafCAN 
Miré los mapas de **Ortofototos territoriales** de Tenerife, para ver la evolución de la zona. 

- 1982 era solamente terreno, sin ninguna infraestructura
- 1987 Se ve un edificio
- 1994 Se ve algún edificio más y la celda de abajo como lisa
- 1998 se ve ya la montaña hecha y escalones, pero solamente trabajaban la celda de abajo.
- 2003 Se ve una balsa con líquido hacia el norte
- 2004 sobre esta fecha yo creo que ya se quedó inactiva la de abajo
- 2006 se ve que donde estaba la balsa, montaron otra celda y la expandieron. Ahora estaban la de abajo antigu ay arriba la nueva.
- 2007 terminaron la montañita de la celda de arriba y empliaron una explanado, justo al lado, pero hacia el norte.
- 2009 en esta explanada nueva, hicieron una montañita fina a la derecha, que fue creciendo hasta 2012
- 2012 la montañita fin se unió con la montaña q había abajo de la explanada
- 2012-2023 Han seguido dejando vertidos en la zona izquierda de la explanada, creciendo la montaña.

- Como esa parte sigue activa, en los datos de EGMS no salen puntos de datos, ya que al haber cambios mecanicos o cambios bruscos debidos a otras cosas, no se puede medir un desplazamiento del suelo.

## Vertedero de Arico

**Video youtube:** https://youtu.be/VVbs-vHz2TE?si=iV-cJlGfwMXimLui

El vertedero de Arico se fundó en 1985. Al principio contaba con 1 millón $m^2$, hace cuatro años, medía ya 2.2 millones $m^2$. Depositan los deshechos que no reciclan, y los van tapando, hasta que llega a una altura límite. Al descomponerse, emiten biogás (Dioxido de Carbono y Metano) que recogen con unos tubos para llevarlo a una planta y convertir el gas en energía. ¿Qué pasa? Que puede haber fugas, puede empezar a descomponerse en el proceso de transportación, depositación y tapación, y además, teniendo en cuenta que el suelo se esta hundiendo, eso fuede provocar ranuras en las telas que lo tapan y huecos en la tierra, por donde salga gas metano. Estas fugas no controladas de emisión de metano, son las que vamos a intentar medir y buscar una correlación con el hundimiendo de las celdas. 


# Dia 4

Intentar descargar los archivos de años anteriores 2016..., pero no lo encontré disponible. Pensar por que puede ser posible qu etodo se desplace hacia un lado. descargar datos ortho desplazamiento horizontal. Comparar los resultados con el ortho y el obtenido anteriormente. 
# Día 5
Hoy no busqué más información, simplemente estuve con python mejorando el código que tenía y dejandolo limpio para poder usar las gráficas y los resultados para la memoria y los documentos futuros. Con esto queda listo las tareas de esta semana.


# Día 6: Optimización de Datos y Superación del "Sesgo Costero" en Tenerife

En esta jornada, hemos estado investigando como funciona el Microsoft Planetary Computer. Aunque no he conseguido ver una forma de programar desde dentro, al final he visto que la forma más cómoda era usar Google Colaboratory, ya que hay una carpeta en la que puedo ir descargando cosas, tomo la información que necesito y luego la elimino. Primero me aseguré de que mi programa podía conectarse a la red de Planetary Computer, haciendo que me dijera cuantas pasadas había hecho el sentinel 5p en un mes. Normalmente hace 35 al mes. Primero busqué los pixeles que había disponibles con un $qa_value$>0.5 y solo me salían en el océano, no me salía ninguno encima de la isla. Probé con $qa_value>0.3$ y salían más puntos pero en la isla un par. Esto puede deberse a:

* **Efecto Costa:** El mar absorbe el infrarrojo, reduciendo la confianza del sensor.
* **Relieve Volcánico:** El desnivel del Teide genera sombras y variaciones de presión que el algoritmo marca como "no fiables".

Inicialmente busqué hacer mapas de calor continuos, pero con la baja ensidad de puntos no tenía mucho sentido, así que lo dejé en un mapa de puntos





# Día 7 

## Uso de Oversampling para Datos TROPOMI

Se han procesado datos del producto **TROPOMI L2 de metano (CH₄)** correspondientes a marzo de 2024 sobre el área de interés centrada en el Complejo Ambiental de Arico (Tenerife). Tras la descarga desde el repositorio del Planetary Computer, se aplicó un filtrado de calidad utilizando el parámetro `qa_value` (≥ 0.3) junto con un recorte espacial sobre la zona de estudio.


La **resolución** nativa del sensor **TROPOMI** es de aproximadamente **7 × 7 km por píxel**, lo cual limita el análisis de fuentes puntuales. Para mejorar la representatividad espacial se aplicó una técnica de **oversampling**:

1. Se definió una **malla regular** de `GRID_RES = 0.01` (~1 km).  
2. Cada píxel de TROPOMI, que se superpone parcialmente con otros, se proyectó sobre las celdas de esta malla.  
3. Para cada celda se calculó el promedio de todos los píxeles que caen en ella:

$$
\text{CH}_4^{\text{celda}} = \frac{1}{N} \sum_{i=1}^{N} \text{CH}_4^i
$$

donde $N$ es el número de observaciones dentro de la celda y $\text{CH}_4^i$ el valor de cada píxel original.

### El oversampling:

- Reduce el **ruido aleatorio** de las mediciones.  
- Mejora la **cobertura espacial** y suaviza el mapa de concentraciones.  
- Permite obtener un **mapa más interpretable** para detectar patrones regionales.
- La **resolución efectiva real** sigue siendo ~7 km; el grid de 1 km no aumenta la resolución instrumental.  
- El oversampling **no genera información nueva**, solo redistribuye y promedia los datos existentes.  
- Oversampling **≠ interpolación**: no se inventan valores, se usan datos reales.

Aunque se use un grid de 1 km, fuentes muy pequeñas (como vertederos o plumas finas) **no pueden detectarse** sin sensores de mayor resolución, como **EMIT (~60 m)** o **GHGSat (~25 m)**.  
Este enfoque es estándar en estudios con TROPOMI y sirve como base para análisis posteriores de anomalías o estimación de flujos de emisión.

# Día 8 

| Característica | TROPOMI (Sentinel-5P) | EMIT (NASA Methane Plume Imaging Spectrometer) |
|----------------|-----------------------|-----------------------------------------------|
| Tipo de sensor | Espectrómetro satelital de absorción atmosférica | Espectrómetro de imágenes de alta resolución |
| Plataforma | Satélite Sentinel-5P | Satélite de la NASA (EMIT) |
| Escala de observación | Regional / global | Local / puntual |
| Resolución espacial | ~7 km × 7 km | ~60 m |
| Cobertura temporal | Global diaria | Limitada a sobrevuelo específico / pluma |
| Qué mide | Concentración de CH₄ en toda la columna atmosférica | Concentración de CH₄ integrada en la columna dentro de plumas |
| Unidad | XCH₄ → partes por millón (ppm) | ppm·m (concentración integrada verticalmente) |
| Datos de nivel | L2 (concentración, qa_value, kernels) | L2B (ppm·m, plumas, métricas) |
| QA / Filtros | qa_value 0–1; filtra nubes, aerosoles, geometría, ajuste de modelo | QA incluido implícitamente en los productos L2B; depende de visibilidad de pluma y calidad espectral |
| Qué permite ver | Variaciones regionales, anomalías de metano | Plumas puntuales, dirección, intensidad y masa de emisiones |
| Limitaciones | No detecta fuentes pequeñas dentro del píxel | Cobertura limitada; depende de que la pluma sea detectable |
| Uso en proyecto | Detección de anomalías y tendencias en Arico (2020–2025) | Cuantificación de emisiones puntuales mediante métricas de plumas |
| Concepto visual | “Cámara panorámica global” | “Lupa sobre la pluma” |
| Procesamiento típico | Filtros QA, máscara de nubes, oversampling (500–1000 m), promedios semanales/mensuales | Extracción de área de pluma, valor máximo, masa integrada, métricas por pluma |
| Escala vertical | Toda la columna atmosférica | Columna completa pero enfocada solo dentro de la pluma detectada |

# Día 9
## Presentación

Voy a escribir aqui alguna idea para el guión:

- **Introdución**: Entre los gases de efecto invernadero encontramos varios compuestos como dióxido de carbono, óxido nitroso y metano.
El metano (CH₄) es especialmente potente, ya que tenemos poca cantidad en la atmosfera, por ello cualquier cambio afecta mucho y sus emisiones pueden incrementarse por diversas fuentes, como el ganado, la industria o los vertederos de residuos sólidos.
En estos vertederos se depositan residuos orgánicos que, al descomponerse, producen metano.
Aunque parte de este gas se recoge mediante sistemas de extracción, pueden producirse emisiones no controladas hacia la atmósfera.



- **EGMS**: EGMS es un servicio de Copernicus que proporciona mapas de movimiento del terreno.
Utiliza datos de radar de satélite mediante técnicas InSAR para medir desplazamientos con alta precisión.
En este caso, nos interesa la subsidencia del vertedero, ya que está relacionada con la compactación y degradación de residuos, procesos que también generan emisiones de metano.

- **Datos memiisones metano**: Para detectar y analizar emisiones de metano se utilizan datos satelitales.
En este trabajo se combinan dos sensores: TROPOMI, que permite observar el metano a escala regional, y EMIT, que permite detectar emisiones puntuales con mayor detalle.


# Día 10 

Presentación


# Día extra semana santa (miercoles 01/04)

Investigar sobre los datos EMIT, y visualizar plumas de emiisones de metano. https://earth.gov/ghgcenter/data-tools/view/emit-plume-viewer
En canarias no se ha detectado ninguna en el visualizador, ya que seguramente las emisiones sean inferiores. Pero no se si habrá alguna forma de tomar los datos que hayan medido para nosotros cuantificar el metano en esa zona, porque el sensor si que tiene frecuentes medidas por encima de Tenerife.

Viendo las plumas de metano en la península, la primera que he observado ha sido la de Murcia, ya que como he vivido allí me ha llamado la atención. Viendo el maps, veo que las emisiones correspondían a la planta de reciclaje de Murcia, al lado de Cañada hermosa.

Solamente hay 3 plumas en toda la península. Otra en Alcázar de San Juan, aunque aquí no he visto ninguna infraesytructura cerca, lo único que está cerca del canal de agua de la depuradora.
