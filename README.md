# Emisiones-de-metano-de-vertederos---Teledeteccion
Proyecto ULL/IPNA-CSIC enfocado en comparar la evolución de las emisiones de metano (CH4) y la compactación del terreno en el vertedero de Arico (2020-2025). Empleando teledetección (TROPOMI, EMIT) y datos EGMS para calcular flujos y analizar su correlación usando Python y QGIS.
# Mi Proyecto de Emisiones - Día 1

## Área de Interés (AOI)
He localizado el Complejo Ambiental de Tenerife (CAT-Arico) y estas son las coordenadas que he obtenido:

- **Latitud:** [28.11769]
- **Longitud:** [16.48005]
- **Ubicación:** Arico, Tenerife.

Para tomar el área de aproximadamente un radio de 10-30 km al rededor de nuestras coordenadas, vamos a tomar un cuadrado sumandole y restandole 22 km en la la latitud y en la longitud. Teniendo en cuenta que 0.1º son unos 11km, si le sumamos y restamos 0.2º, obtenemos un cuadrado de 44km de alto y 44km de ancho. Por lo que, tendremos:

-**Latitud Norte (Lat + 0.2º)**: [28.31769]
-**Latitud Sur (Lat - 0.2º)**: [27.91769]
-**Longitud Este (Long + 0.2º)**: [16.68005]
-**Longitud Oeste (Long - 0.2º)**: [16.28005]
