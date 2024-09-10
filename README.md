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
El objetivo de este análisis es refutar o confirmar 5 hipótesis sobre qué hace que una canción sea más escuchada.

## Hipótesis
1. Las canciones con un mayor BPM (Beats Por Minuto) tienen más éxito en términos de cantidad de streams en Spotify.
2. Las canciones más populares en el ranking de Spotify también tienen un comportamiento similar en otras plataformas como Deezer.
3. La presencia de una canción en un mayor número de playlists se relaciona con un mayor número de streams. 
4. Los artistas con un mayor número de canciones en Spotify tienen más streams.
5. Las características de la canción influyen en el éxito en términos de cantidad de streams en Spotify. 




## Herramienetas

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


## Hallazgos principales


- La distribución del histograma de streams está inclinada hacia la izquierda, lo que significa que hay un pequeño número de canciones que poseen alta cantidad de streams. Estas canciones son las que se consideran "éxitos" en plataforma.
![image](https://github.com/user-attachments/assets/3848e1fa-b20e-4334-b89a-ef3c9b69252d)


- Los 5 artistas con mayor canciones y mayor streams son *solistas*

![image](https://github.com/user-attachments/assets/b1fee412-0f83-47bc-9758-f0505164a119) ![image](https://github.com/user-attachments/assets/909e4833-54b3-4b27-b3bc-43fa9448f802)


- El día que tiene más canciones de lanzamiento son los días 7, 9 y 21 del mes
![image](https://github.com/user-attachments/assets/41b77e1d-febc-4e73-a782-aa6e98c634ff)



## Resultados

### Validación de hipótesis

#### 1. BPM
Los BPM (Beats Por Minuto) **no tienen influencia** sobre el éxito de las canciones en términos de cantidad de streams en Spotify.

![image](https://github.com/user-attachments/assets/e2061c48-707d-4dac-95f1-5620bade8a1e)


#### 2. Características técnicas

Las características técnicas *no influyen generalmente* en la cantidad de streams. Pero sí debemos considerar las características en común de las canciones más escuchadas de los artistas.

![image](https://github.com/user-attachments/assets/9688944e-7e1f-4c34-b6cf-24a717bf8fe8)


#### 3. Charts

Existe una tendencia positiva en la comparación de listas en Spotify vs Deezer.

![image](https://github.com/user-attachments/assets/72ae779b-77ed-4747-b58b-95400cfec15e)


#### 4. Playlists

Las presencia de una canción en más de una plataforma de streaming no necesariamente significa una mayor cantidad de streams, ya que canción About Damn Things sólo se encuentra en 1 plataforma e iguala los streams de las canciones que están en más plataformas.
![image](https://github.com/user-attachments/assets/6333784d-80dc-490a-aade-e40253391ebb)


#### 5. Cantidad de canciones

Se valida hipótesis, ya que mayor cantidad de canciones, se comporta de igual forma respecto a la cantidad de streams según gráfico.

![image](https://github.com/user-attachments/assets/1400389a-5188-4ecd-820a-b303b87c2111)

## Recomendaciones

- 1. Se recomienda considerar características de canciones según cantidad de streams en plataforma, y en base a artistas líderes.
- 2. El mes que más posee lanzamientos es en enero, en los días 7, 9 o 21 de cada mes.
- 3. Los solistas son quienes más éxito tienen en comparación a duetos y grupos.


   ## LINKS DE INTERÉS

  PDF DE DASHBOARD: 
