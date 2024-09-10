# PROCESAMIENTO DE DATOS EN BIG QUERY

## Contenido

**Tabla de contenido**

- [Nulls](#nulls)
- [Duplicados](#duplicados)
- [Datos fuera del alcance](#datosfueradelalcance)
- [Datos discrepantes](#datosdiscrepantes)
- [Cambiar tipo de dato](#cambiartipodedato)
- [Nuevas variables](#nuevasvariables)
- [Unión de tablas](#unióndetablas)
- [Cuartiles](#cálculodecuartiles)
- [Correlación de Pearson](#correlacióndepearson)


## NULLS

Se identificaron 50 NULLS en la tabla competition, los cuales se mantuvieron en la base de datos:

```sql
SELECT 
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE track_id IS NULL) AS null_id,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_apple_playlists IS NULL) AS null_apple_playlists,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_apple_charts IS NULL) AS null_apple_charts,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_deezer_playlists IS NULL) AS null_deezer,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_deezer_charts IS NULL) AS null_deezer_charts,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_shazam_charts IS NULL) AS null_shazam_charts,
```

Identificación de NULLS en tabla TECHNICAL_INFO, los cuales se mantuveron en la base de datos:

```sql

SELECT 
*
FROM `proyecto-laboratoria-hipotesis.dataset_hipotesis. technical_info`
WHERE
  `key` IS NULL
```


## DUPLICADOS

Identificación de datos duplicados en tabla SPOTIFY:

```sql
SELECT
  track_name,
  artist_s__name,
  COUNT(*) AS cantidad
FROM
  `proyecto-laboratoria-hipotesis.dataset_hipotesis.spotify`
GROUP BY
  track_name,
  artist_s__name
HAVING
  COUNT(*) > 1;

```
## DATOS FUERA DEL ALCANCE

Esta QUERY elimina las columnas KEY y MODE de la tabla TECHNICAL INFO:

```sql

SELECT 
* EXCEPT (`key`, mode)
FROM `proyecto-laboratoria-hipotesis.dataset_hipotesis. technical_info` 

```

## DATOS DISCREPANTES 

### Variables categóricas 

Se identificó un dato discrepante en la columna STREAMS de la tabla SPOTIFY: 

```sql

SELECT * FROM `proyecto-laboratoria-hipotesis.dataset_hipotesis.spotify` 
WHERE streams = "BPM110KeyAModeMajorDanceability53Valence75Energy69Acousticness7Instrumentalness0Liveness17Speechiness3"

```

### Variables numéricas

STREAMS 

```sql

SELECT
  MAX(streams_int) AS max_streams,
  MIN(streams_int) AS min_streams,
  AVG(streams_int) AS avg_streams
FROM (
  SELECT 
    CAST(streams AS INT64) AS streams_INT
  FROM 
    `proyecto-laboratoria-hipotesis.dataset_hipotesis.spotify`
  WHERE 
    REGEXP_CONTAINS(streams, r'^[0-9]+$')

```

## CAMBIAR TIPO DE DATO

Esta QUERY convierte la variable STREAMS de un dato STRING a INTERGER; y elimina el valor discrepante: 

```sql

SELECT 
  *,
  CAST(streams AS INT64) AS streams_INT
FROM 
  `proyecto-laboratoria-hipotesis.dataset_hipotesis.spotify`
WHERE 
  REGEXP_CONTAINS(streams, r'^[0-9]+$');

```

## NUEVAS VARIABLES

Creación de nueva variable de fecha de lanzamiento de las canciones:

```sql

SELECT 
track_id, track_name, artist_s__name,
  PARSE_DATE('%Y-%m-%d', CONCAT(
    CAST(released_year AS STRING), '-', 
    LPAD(CAST(released_month AS STRING), 2, '0'), '-', 
    LPAD(CAST(released_day AS STRING), 2, '0')
  )) AS release_date
FROM 
  `proyecto-laboratoria-hipotesis.dataset_hipotesis.spotify`

```

## UNIÓN DE TABLAS

VIEW de
- Unión de tablas: SPOTIFY, TECHNICAL INFO y COMPETITION a través de LEFT JOIN,
- Creación de nueva variable de PARTICIPACIÓN EN PLAYLIST a través de SUMA,
- Creación de tabla auxiliar de TOTAL DE CANCIONES por artista a través de WITH:

```sql

WITH total_canciones_por_artista AS (
  SELECT 
    COUNT(*) AS total_canciones, 
    artist_s_name
  FROM
    `proyecto-laboratoria-hipotesis.dataset_hipotesis.spotify_cleaned` 
  WHERE 
    artist_count = 1  -- Filtrar solo canciones con un solo artista
  GROUP BY 
    artist_s_name
)

SELECT 
  A.track_id,
  A.track_name,
  A.artist_s_name,
  A.release_date,
  A.streams,
  A.artist_count,
  A.released_year,
  A.released_month,
  A.released_day,
  A.in_spotify_playlists,
  A.in_spotify_charts,
  B.bpm,
  B.`danceability_%`,
  B.`valence_%`,
  B.`energy_%`,
  B.`acousticness_%`,
  B.`instrumentalness_%`,
  B.`liveness_%`,
  B.`speechiness_%`,
  C.in_apple_playlists,
  C.in_apple_charts,
  C.in_deezer_playlists,
  C.in_deezer_charts,
  C.in_shazam_charts,
  t.total_canciones,
  A.in_spotify_playlists + C.in_apple_playlists + C.in_deezer_playlists AS total_playlists  -- Nueva columna SUMA DE IN PLAYLISTS
FROM 
  `proyecto-laboratoria-hipotesis.dataset_hipotesis.spotify_cleaned` AS A
LEFT JOIN (
  SELECT 
    track_id,
    bpm,
    `danceability_%`,
    `valence_%`,
    `energy_%`,
    `acousticness_%`,
    `instrumentalness_%`,
    `liveness_%`,
    `speechiness_%`
  FROM `proyecto-laboratoria-hipotesis.dataset_hipotesis.view_info_tecnica_limpia`
) AS B
ON A.track_id = B.track_id
LEFT JOIN (
  SELECT 
    track_id,
    in_apple_playlists,
    in_apple_charts,
    in_deezer_playlists,
    in_deezer_charts,
    in_shazam_charts
  FROM `proyecto-laboratoria-hipotesis.dataset_hipotesis.competition`
) AS C
ON A.track_id = C.track_id
LEFT JOIN
  total_canciones_por_artista AS t
ON A.artist_s_name = t.artist_s_name;

```

## CÁLCULO DE CUARTILES

Cálculo de cuartiles a través de la función NTILE(4) OVER (ORDER BY___):

```sql

SELECT 
  track_id,
  track_name,
  artist_s_name,
  release_date,
  streams,
  total_playlists,
  artist_count,
  released_year,
  released_month,
  released_day,
  total_canciones,
  in_spotify_playlists,
  in_spotify_charts,
  bpm,
  `danceability_%`,
  `valence_%`,
  `energy_%`,
  `acousticness_%`,
  `instrumentalness_%`,
  `liveness_%`,
  `speechiness_%`,
  in_apple_playlists,
  in_apple_charts,
  in_deezer_playlists,
  in_deezer_charts,
  in_shazam_charts,
  NTILE(4) OVER (ORDER BY streams) AS quartile_streams,
  NTILE(4) OVER (ORDER BY total_playlists) AS quartile_total_playlists,
  NTILE(4) OVER (ORDER BY bpm) AS quartile_bpm,
  NTILE(4) OVER (ORDER BY `danceability_%`) AS quartile_danceability,
  NTILE(4) OVER (ORDER BY `valence_%`) AS quartile_valence,
  NTILE(4) OVER (ORDER BY `energy_%`) AS quartile_energy,
  NTILE(4) OVER (ORDER BY `acousticness_%`) AS quartile_acousticness,
  NTILE(4) OVER (ORDER BY `instrumentalness_%`) AS quartile_instrumentalness,
  NTILE(4) OVER (ORDER BY `liveness_%`) AS quartile_liveness,
  NTILE(4) OVER (ORDER BY `speechiness_%`) AS quartile_speechiness
FROM 
  proyecto-laboratoria-hipotesis.dataset_hipotesis.view_tabla_completa_hipotesis

```

# CORRELACIÓN DE PEARSON

Calcular de la correlación entre dos variables continuas, STREAMS y características técnicas:

```sql

SELECT 
    CORR(streams, bpm) AS st_bpm_corr,
    CORR(streams, `danceability_%`) AS str_danceability_corr,
    CORR(streams, `valence_%`) AS str_valence_corr,
    CORR(streams, `energy_%`) AS str_energy_corr,
    CORR(streams, `acousticness_%`) AS str_acousticness_corr,
    CORR(streams, `instrumentalness_%`) AS str_instrumentalness_corr,
    CORR(streams, `liveness_%`) AS str_liveness_corr,
    CORR(streams, `speechiness_%`) AS str_speechiness_corr,

    FROM `proyecto-laboratoria-hipotesis.dataset_hipotesis.view_tabla_completa_hipotesis`

```

Correlación entre STREAMS y aparación de canciones en PLAYLISTS:

```sql

SELECT CORR(streams, total_playlists) AS correlacion_S_P
FROM `proyecto-laboratoria-hipotesis.dataset_hipotesis.view_tabla_completa_hipotesis`

```

### Normalización de CHARTS

```sql

SELECT 
    track_id,
    track_name,
    (in_spotify_charts - MIN(in_spotify_charts) OVER()) / (MAX(in_spotify_charts) OVER() - MIN(in_spotify_charts) OVER()) AS normalized_spotify_charts,
    (in_deezer_charts - MIN(in_deezer_charts) OVER()) / (MAX(in_deezer_charts) OVER() - MIN(in_deezer_charts) OVER()) AS normalized_deezer_charts,
    (in_shazam_charts - MIN(in_shazam_charts) OVER()) / (MAX(in_shazam_charts) OVER() - MIN(in_shazam_charts) OVER()) AS normalized_shazam_charts,
    (in_apple_charts - MIN(in_apple_charts) OVER()) / (MAX(in_apple_charts) OVER() - MIN(in_apple_charts) OVER()) AS normalized_apple_charts
FROM 
   `proyecto-laboratoria-hipotesis.dataset_hipotesis.view_tabla_completa_hipotesis`
WHERE 
    in_spotify_charts IS NOT NULL AND
    in_deezer_charts IS NOT NULL AND
    in_shazam_charts IS NOT NULL AND
    in_apple_charts IS NOT NULL;

```

### Correlación de CHARTS en Spotify vs otras plataformas:

```sql

SELECT 
    CORR(normalized_spotify_charts, normalized_deezer_charts) AS spotify_deezer_corr,
    CORR(normalized_spotify_charts, normalized_shazam_charts) AS spotify_shazam_corr,
    CORR(normalized_spotify_charts, normalized_apple_charts) AS spotify_apple_corr
FROM 
    `proyecto-laboratoria-hipotesis.dataset_hipotesis.view_charts_normalizados`;

```
