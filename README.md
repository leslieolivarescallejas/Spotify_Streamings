# Spotify_Streamings
Este proyecto de análisis de datos busca refutar o confirmar 5 hipótesis sobre el comportamiento musical en 3 diferentes plataformas de streaming.
## Content
**Tabla de contenido**

- [Introducción](#introducción)
- [Hipótesis](#hipótesis)
- [Herramientas](#herramientas)
- [Proceso](#proceso)
- [Características de la muestra](#característicasdelamuestra)
- [Hallazgos principales](#hallazgosprincipales)
- [Resultados](#resultados)
- [Conclusiones](#conclusiones)
- [Links de interés](#linksdeinterés)

## Introducción
rellenar
## Hipótesis
rellenar 
## Herramienetas
rellenar

# Hipotesis_Streaming

Este proyecto de análisis de datos busca comprobar o descartar cinco hipótesis sobre los factores que podrían influir en el éxito de una canción en plataformas de streaming como Spotify. 

## Contenido

**Tabla de contenido**

- [Introducción](#introducción)
- [Hipótesis](#hipótesis)
- [Herramientas](#herramientas)
- [Proceso](#proceso)
- [Características de la muestra](#característicasdelamuestra)
- [Hallazgos principales](#hallazgosprincipales)
- [Resultados](#resultados)
- [Conclusiones](#conclusiones)
- [Links de interés](#linksdeinterés)
  

## Introducción 

Las plataformas de streaming marcaron un antes y un después en la industria musical, estos servicios permiten a los artistas llegar a una audiencia global de manera instantánea, facilitan la difusión y popularidad de sus temas. Pero, ¿cómo lo logran? En este proyecto se busca explorar el comportamiento de la industria en los últimos años para encontrar patrones que puedan influir en la popularidad de las canciones.

## Hipótesis

Hipótesis planteadas sobre qué hace que una canción sea más escuchada:
1. Las canciones con un mayor BPM (Beats Por Minuto) tienen más éxito en términos de cantidad de streams en Spotify.
2. Las canciones más populares en el ranking de Spotify también tienen un comportamiento similar en otras plataformas como Deezer.
3. La presencia de una canción en un mayor número de playlists se relaciona con un mayor número de streams.
4. Los artistas con un mayor número de canciones en Spotify tienen más streams.
5. Las características de la canción influyen en el éxito en términos de cantidad de streams en Spotify.

## Herramientas

- SQL (Google BigQuery)
- Python
- Power BI

## Proceso

- Importación de dataset a BigQuery.
- Identificación de NULOS con WHERE, COUNT, IS NULL.
- Identificación de duplicados. 
- Eliminación de variables fuera del alcance por medio de EXCEPT.
- Eliminación de datos discrepantes en variables categoricas.
- Modificación de formato de datos (string a interger) para mejor procesamiento por medio de CAST.
- Creación de nuevas variables: fecha de lanzamiento y cantidad de canciones por artista.
- Unión de tablas con LEFT JOIN.
- Creación de tablas auxiliares con WITH.
- Segmentación de canciones.
- Cálculo de correlación entre variables.

## Características de la muestra

<img width="709" alt="image" src="https://github.com/user-attachments/assets/741bddff-6e7b-46ea-9830-2e3c7b03b9f7">

## Hallazgos principales

Las canciones de la muestra tienen un **promedio de 514 millones de streams.** 

La distribución del histograma de streams está sesgada hacia la izquierda, lo que significa que hay un pequeño número de canciones que acumulan un gran número de streams. Estas canciones son las que se consideran "éxitos" en plataforma.

<img width="521" alt="image" src="https://github.com/user-attachments/assets/0dc68f42-79c3-4795-8b4f-0fc162ede01a">

La canción más escuchada registra **7.2 veces más streams que el promedio** de la muestra.

<img width="415" alt="image" src="https://github.com/user-attachments/assets/22807a57-2143-4270-a6ec-843d2f6352b3">


Destaca que, de los cinco artistas más escuchados, **sólo una es mujer:** Taylor Swift.

<img width="415" alt="image" src="https://github.com/user-attachments/assets/2148f510-66c9-4aa4-bdb2-fdf27f117439">

Del 2019 al 2022, se registra una mayor cantidad de lanzamientos en el cuarto trimestre del año (Spotify); sin embargo, el pico de streams en se observa en el segundo trimestre.

<img width="411" alt="image" src="https://github.com/user-attachments/assets/0d5c42af-0c35-4292-948f-57f10995e10e">

## Resultados

### Validación de hipótesis

#### 1. BPM
Los BPM (Beats Por Minuto) **no tienen influencia** sobre el éxito de las canciones en términos de cantidad de streams en Spotify.

<img width="427" alt="image" src="https://github.com/user-attachments/assets/559d823b-7385-4b45-a4b3-61cf135e70d1">


<img width="403" alt="image" src="https://github.com/user-attachments/assets/eda5abb9-cd68-45af-8b77-3f2c5c531933">

#### 2. Características técnicas

Las características técnicas **no necesariamente influyen** en el éxito en términos de cantidad de streams. Entre las características evaluadas, **danceability** tendría un mayor impacto, ya que las canciones con un nivel alto registran 28.91% menos streams (promedio).

<img width="761" alt="image" src="https://github.com/user-attachments/assets/802d1f18-9ec9-449e-8ea5-18ca9c04e958">

#### 3. Charts

Existe una **correlación positiva moderada** entre la posición en charts de una canción en Spotify y su comportamiento en charts de otras plataformas como Apple, aunque esta relación no es perfecta y podrían existir otros factores que impactan en la popularidad. 

<img width="511" alt="image" src="https://github.com/user-attachments/assets/c388543c-b342-4fd8-8c6c-54c7fb617430">

#### 4. Playlists

La presencia de una canción en un mayor número de playlists **sí tiene una relación positiva marcada** con un mayor número de streams.

<img width="509" alt="image" src="https://github.com/user-attachments/assets/c1fc1a9c-4d0f-4fdc-a9a4-634acda5fc47">

#### 5. Cantidad de canciones

Se confirma que los artistas con un mayor número de canciones en Spotify registran más streams.

<img width="707" alt="image" src="https://github.com/user-attachments/assets/08b4ac10-4c2f-4981-9480-a4cc4ecfdec4">

## Conclusiones

- Se debe enfocar esfuerzo en la  visibilidad y promoción de canciones a través de múltiples playlists, ya que tienen un impacto considerable en su éxito en relación con las reproducciones.

- Se recomienda que los artistas se mantengan activos en el lanzamiento de nuevos temas, esto influye en su popularidad y cantidad de streams.

- Las características técnicas no son determinantes para la cantidad de reproducciones de un tema, pero se podría tomar en cuenta el nivel de danceability al ser el rasgo con mayor influencia. 

- Si bien, la aparición en charts de Spotify tiene una relación moderada con el comportamiento de las canciones en otras plataformas, se recomienda integrar este aspecto en la estrategia de lanzamiento debido a la exposición y la percepción de popularidad que genera.


   ## LINKS DE INTERÉS

  PDF DE DASHBOARD: 
