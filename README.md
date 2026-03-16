# Emisiones-de-metano-de-vertederos---Teledeteccion
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

-**TROPOMI**: TROPOspheric monitoring instrument, es el instrumento (sensor o cámara) que va  aborde del sentinel-5p de la ESA. Es un espectrómetro que mide gases en la atmósfera (por ejemplo el métano). Tiene una alta resolución, por eso nos permitirá detectar fuentes de emisión de vertederos. 

-**PUM**: Product User Manual, es el manual de instrucciones de los archivos que contienen la información. Nos indica que carpetas hay en el archivo, como filtrar datos malos (qa_value), nos indica en que unidades vien ele metano...

Entonces, sobre TROPOMI, según el PUM, se deben monitorizar los parámetros de Quality Assurance. Para el análisi de metano, el umbral recomendado para el filtrado de datos suele ser qa_value > 0.5. 

En cuanto al Average Kernel: el satelite captura mejor la información de la estratosfera, y peor la atmosfera cercana al suelo. El Average Kernel indica cuanta sensibilidad tiene el satélite en cada capa de la atmósfera según la altura. 
- **Kernel 1**: el satelite ve perfectamente lo que ocurre en la capa.
- **Kernel 0**: no ve nada

También es importante hablar de los niveles. Estos indican como de procesado está el dato que toma el satélite.

- **Nivel 0 (L0)**: Dato crudo.
- **Nivel 1 (L1)**: Es el dato geolocalizado y calibrado. La luz se convierte a unidades de energía. L1B te indica cuanta luz de cada color llegó al sensor.
- **Nivel 2 (L2)**: Es el dato geofísico. Un software analiza la luz de L1 y calcula la cantidad de moléculas que hay de un gas específicio. Caso TROPOMI L2 CH_4, nos da directamente la concentración de metano en partes por mil millones (ppb) para cada pixel de mi AOI. Esto es loq ue descargaremos y analizaremos en python (?).
Nivel 3 (L3): Dato mapeado y promediado. L2 son tiras de datos que tomó ese día el satlélite. L3 coge los datos de varios días y se oclocan en un mapa limpio. Se pueden perder fugas de metano.

- 
