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

NULLS para Competition: 50 en Shazam Charts. Se mantienen.

```sql
SELECT 
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE track_id IS NULL) AS null_id,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_apple_playlists IS NULL) AS null_apple_playlists,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_apple_charts IS NULL) AS null_apple_charts,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_deezer_playlists IS NULL) AS null_deezer,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_deezer_charts IS NULL) AS null_deezer_charts,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyCompetition.AnotherPlatforms` WHERE in_shazam_charts IS NULL) AS null_shazam_charts,
```

NULLS para Technical Info: Key. Se mantienen.

```sql
SELECT 
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE track_id IS NULL) AS null_id,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE bpm IS NULL) AS null_bpm,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE key IS NULL) AS null_key,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE mode IS NULL) AS null_mode,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE `danceability_%` IS NULL) AS null_dance,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE `valence_%` IS NULL) AS null_valence,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE `energy_%` IS NULL) AS null_energy,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE `acousticness_%` IS NULL) AS null_acoustic,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE `instrumentalness_%` IS NULL) AS null_instrumental,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE `liveness_%` IS NULL) AS null_liveness,
  (SELECT COUNT(*) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongs` WHERE `speechiness_%` IS NULL) AS null_speechiness,

```


## DUPLICADOS

Identificación de datos duplicados en tabla SPOTIFY:

```sql
SELECT 
 track_name,
 artists_name,
 COUNT(*) as duplicados
FROM `dataset-spotify.SpotifyTrackIn.StreamsCLEAN`
GROUP BY track_name, artists_name
HAVING COUNT(*) > 1

```


## DATOS FUERA DEL ALCANCE

Para eliminar datos innecesarios tales como KEY y MODE de la tabla TECHNICAL INFO ya que tienen mucha cantidad de nulos:

```sql

SELECT * EXCEPT(mode, key) FROM `dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongsClean` 

```



## DATOS DISCREPANTES 

### Variables categóricas 

Se corrigen los carácteres especiales de las canciones y artistas:


```sql

SELECT 
 track_name,
 REGEXP_REPLACE(track_name, r'[^a-zA-Z0-9]', ' ') AS track_name_clean,
FROM `dataset-spotify.SpotifyTrackIn.StreamsCLEAN`


```



### Variables numéricas

STREAMS 

```sql

SELECT 
MAX(streams_integer) as MAXIMO,
MIN(streams_integer) AS MINIMUM,
AVG(streams_integer) AS AVERAGE
FROM `dataset-spotify.SpotifyTrackIn.StreamsCLEAN2`

```



## CAMBIAR TIPO DE DATO

Necesitamos convertir datos STRINGS a INTEGER para tener lectura de números y no sólo letras: 

```sql

SELECT SAFE_CAST(streams AS INT64) AS streams_clean_inter
FROM `dataset-spotify.SpotifyTrackIn.Streams` 
WHERE REGEXP_CONTAINS(streams, r'^[0-9]+$');

```



## NUEVAS VARIABLES

Se crea la variable sumatoria de Playlists:

```sql

SELECT track_id, in_apple_playlists, in_deezer_playlists, in_spotify_playlists, in_deezer_playlists+in_apple_playlists+in_spotify_playlists AS SUM_PLAYLIST FROM `dataset-spotify.datasetspotify2.leftjoin-alltables-view`

```



## UNIÓN DE TABLAS

Se crea una VIEW de Unión de tablas a través de left join:
```sql
CREATE VIEW dataset-spotify.`SpotifyTrackIn.StreamsTechCompJOINLEFTVIEW` as 
SELECT C.*, T.* EXCEPT(track_id), S.* EXCEPT(track_id)
FROM `dataset-spotify.SpotifyCompetition.CompetitionCleaned` AS C
LEFT JOIN
`dataset-spotify.SpotifyTechnicalInfo.TechnicalInfoSongsClean` AS T
ON C.track_id = T.track_id
LEFT JOIN
`dataset-spotify.SpotifyTrackIn.StreamsCleaned` AS S
ON 
C.track_id = S.track_id

```

  
- Se utiliza comando WITH para crear tabla auciliar de canciones por artista:
  
```sql
 WITH songs_by_artists AS (
  SELECT 
    COUNT(*) AS songs_by_artist, 
    artist_name_clean
  FROM
    `dataset-spotify.SpotifyTrackIn.StreamsTechCompDataset`
  WHERE 
    artist_count = 1  -- Filtrar solo canciones con un solo artista
  GROUP BY 
    artist_name_clean
)
SELECT 
  songs_by_artist, 
  artist_name_clean
FROM 
  songs_by_artists;
```

- Se crea nueva tabla con nueva variable de sumatoria de playlists
```sql
CREATE TABLE `dataset-spotify.SpotifyTrackIn.DatasetStreamsTechCompSum` AS
SELECT *, in_deezer_playlists+in_apple_playlists+in_spotify_playlists AS SUM_PLAYLIST 
FROM `dataset-spotify.SpotifyTrackIn.StreamsTechCompDataset`
```



## CÁLCULO DE CUARTILES

Cálculo de cuartiles a través de la función NTILE(4) OVER (ORDER BY___):

```sql

WITH DatasetWithQuartiles AS (
  SELECT a.*,
         NTILE(4) OVER (ORDER BY a.streams) AS quartile_STREAMS
  FROM `dataset-spotify.SpotifyTrackIn.DatasetStreamsTechCompSum` a
)

SELECT a.*,
       DatasetWithQuartiles.quartile_STREAMS,
       IF(DatasetWithQuartiles.quartile_STREAMS = 4, 'alto', 'bajo') AS categ_STREAMS
FROM `dataset-spotify.SpotifyTrackIn.DatasetStreamsTechCompSum` a
LEFT JOIN DatasetWithQuartiles
ON a.streams = DatasetWithQuartiles.streams;

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

    FROM `dataset-spotify.SpotifyTrackIn.DatasetStreamsTechCompSum`

```

Correlación entre STREAMS y aparación de canciones en PLAYLISTS:

```sql

SELECT 
CORR(streams, SUM_PLAYLIST) AS CorrStreamsPlaylists
FROM `dataset-spotify.SpotifyTrackIn.DatasetStreamsTechCompSum`

```
