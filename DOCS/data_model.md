# Modelo de datos (normalizado) y reglas de transformación

Este documento define el modelo canónico que usa la aplicación para persistir datos obtenidos de TMDB y las reglas de transformación aplicadas durante el ETL.

## Entidades principales

1. Movie
   - id (int) — TMDB id (PK)
   - title (string)
   - original_title (string)
   - overview (text)
   - release_date (date|null)
   - runtime (int|null)
   - language (string, ISO 639-1)
   - popularity (float)
   - vote_average (float)
   - vote_count (int)
   - poster_path (string|null) — almacena sólo el path retornado por TMDB
   - backdrop_path (string|null)
   - created_at, updated_at (timestamps)

2. Genre
   - id (int) — TMDB id (PK)
   - name (string)

3. MovieGenre (relación N:M)
   - movie_id (FK -> Movie.id)
   - genre_id (FK -> Genre.id)

4. Person
   - id (int) — TMDB id (PK)
   - name (string)
   - biography (text|null)
   - birthday (date|null)
   - deathday (date|null)
   - place_of_birth (string|null)
   - profile_path (string|null)

5. Credit (credits normalizados)
   - id (composite or surrogate)
   - movie_id (FK)
   - person_id (FK)
   - role_type (enum: CAST, CREW)
   - character (string|null)
   - job (string|null)
   - order (int|null)

6. Image (opcional, cache de imágenes)
   - id (auto)
   - tmdb_path (string)
   - width, height (int|null)
   - type (poster/backdrop/profile)

## Reglas de normalización y transformación

- Identificadores
  - Usar siempre los ids de TMDB como claves primarias externas. No re-hashear ni crear ids nuevos salvo en tablas intermedias.

- Fechas
  - Parsear `YYYY-MM-DD` a tipo date. Si formato inválido o vacío -> NULL.

- Texto y localización
  - Guardar `title` y `overview` en el idioma recibido por la llamada. Las traducciones se modelan en tablas separadas si son necesarias (MovieTranslation).

- Imágenes
  - Almacenar sólo el `path` (ej.: `/abc.jpg`) en `poster_path`. Construir URLs completas en la capa de presentación usando la `configuration` de TMDB (secure_base_url + size + path).

- Populares y ratings
  - Conservar `popularity`, `vote_average` y `vote_count` tal cual. Para comparaciones entre fuentes, normalizar el `vote_average` a escala 0-10 si otra fuente usa diferente.

- Deduplificación
  - Upsert por `id` de TMDB. Solo actualizar campos cuando el valor nuevo no sea NULL o se considere más reciente (usar `updated_at` del fetcher).

- Relaciones
  - Mantener relaciones N:M normalizadas (MovieGenre, Credit).

## Contrato de importación (ETL)

- Input: JSON desde TMDB.
- Output: inserciones/actualizaciones en tablas normalizadas descritas arriba.
- Errores: registrar en log y continuar; para fallos no transaccionales (pérdida de parte de datos), marcar registro con flag `incomplete=true` y enviar alerta si es crítico.

## Casos especiales

- Películas sin `release_date`: almacenar NULL y priorizar búsquedas por título.
- Personas con múltiples `place_of_birth` inconsistentes: conservar original y añadir `notes` en la tabla si se detecta conflicto.

## Notas finales

Este modelo prioriza integridad y facilidad de consulta (read-side). Para optimizaciones de lectura (dashboards, búsqueda) considerar materialized views o índices específicos (title trigram, release_date, popularity).