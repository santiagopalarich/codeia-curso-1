# Endpoints TMDB

Resumen de endpoints usados en el proyecto, límites y ejemplos JSON.

> Nota: Siempre verificar la documentación oficial en https://developers.themoviedb.org/ para límites y cambios de API.

## Endpoints principales

- GET /configuration
  - Uso: Obtener base URLs y tamaños de imágenes.
  - Parámetros: ninguno
  - Límite: sujeto a la política de TMDB (ver nota)
  - Ejemplo de respuesta (parcial):

```json
{
  "images": {
    "base_url": "http://image.tmdb.org/t/p/",
    "secure_base_url": "https://image.tmdb.org/t/p/",
    "poster_sizes": ["w92","w154","w185","w342","w500","w780","original"]
  },
  "change_keys": ["adult","also_known_as","biography"]
}
```

- GET /movie/{movie_id}
  - Uso: Obtener detalles de una película por ID
  - Parámetros: append_to_response (ej.: `credits,images`)
  - Ejemplo de respuesta (parcial):

```json
{
  "id": 550,
  "title": "Fight Club",
  "release_date": "1999-10-15",
  "genres": [{"id": 18, "name": "Drama"}],
  "poster_path": "/bptfVGEQuv6vDTIMVCHjJ9Dz8PX.jpg"
}
```

- GET /search/movie
  - Uso: Búsqueda de películas
  - Parámetros: query (string), page (int), include_adult (bool)
  - Ejemplo de respuesta (parcial):

```json
{
  "page": 1,
  "total_results": 1,
  "results": [
    {"id": 550, "title": "Fight Club", "release_date": "1999-10-15"}
  ]
}
```

- GET /discover/movie
  - Uso: Búsquedas avanzadas por filtros (año, certificación, género, ordenamiento)
  - Parámetros: with_genres, primary_release_year, sort_by, page

- GET /person/{person_id}
  - Uso: Obtener detalles de una persona (actor/crew)
  - Parámetros: append_to_response (ej.: `movie_credits`)

## Límites y buenas prácticas

- TMDB aplica límites por IP y/o por API key. Revisar la doc oficial antes de diseñar concurrencia.
- Recomendación del proyecto:
  - Usar una capa de rate limiter en el fetcher (ej.: token-bucket o leaky-bucket).
  - Agrupar requests con `append_to_response` cuando sea posible para reducir llamadas.
  - Cachear respuestas no dinámicas (configuration, genres) durante 24h o más.

## Ejemplos de uso común

- Construir URL de poster:
  - base_url (configuration.secure_base_url) + size (ej. `w342`) + poster_path
  - ej.: `https://image.tmdb.org/t/p/w342/bptfVGEQuv6vDTIMVCHjJ9Dz8PX.jpg`

- Obtener créditos junto al detalle de una película:
  - GET /movie/{id}?append_to_response=credits,images

## Notas

- Este documento resume los endpoints que usamos en la mayoría de las integraciones. Para cualquier caso especial (rate limits por plan, IPs o acuerdos comerciales) referirse al soporte y la documentación oficial de TMDB.