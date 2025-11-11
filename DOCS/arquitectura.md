# Arquitectura (legacy)

Este documento describe la arquitectura general del proyecto y las responsabilidades de cada componente — señalando que parte proviene de código/decisiones legacy.

## Diagrama (simplificado)

```
     +-------------+        +-------------+        +---------------+
     |  Scheduler  | -----> |   Fetcher   | -----> |   Normalizer  |
     | (cron/jobs) |        |  (TMDB API) |        |  (ETL rules)  |
     +-------------+        +-------------+        +---------------+
                                     |                    |
                                     v                    v
                              +----------------+    +-------------+
                              |   Cache / CDN  |    |   Database  |
                              |  (images, ttl) |    |  (Postgres) |
                              +----------------+    +-------------+
                                         |               |
                                         v               v
                                    +----------------------------+
                                    |        API Server          |
                                    |  (REST / GraphQL / Auth)   |
                                    +----------------------------+
                                                |
                                                v
                                           +-----------+
                                           | Frontend  |
                                           +-----------+
```

## Componentes y responsabilidades

- Scheduler
  - Ejecuta jobs periódicos: recolección de películas nuevas, actualización de metadata, refresco de caches.
  - Legacy: algunos cronjobs antiguos siguen ejecutando scripts en disco; plan de migración pendiente.

- Fetcher (TMDB client)
  - Llamadas externas a TMDB, aplica rate limiter y retries.
  - Responsable de serializar respuestas crudas a un formato intermedio.

- Normalizer (ETL)
  - Aplica las reglas de transformación descritas en `data_model.md`, realiza upserts en la base de datos.
  - Legacy: contiene scripts duplicados con transformaciones antiguas — hay que consolidar.

- Cache / CDN
  - Almacena imágenes y resultados pesados. TTL configurable.

- Database
  - Almacena el modelo normalizado (preferible: PostgreSQL). Indexes y materialized views para consultas pesadas.

- API Server
  - Expone endpoints para el frontend y terceros. Maneja autenticación, paginación y límites.
  - Responsabilidad adicional: traducir `poster_path` a URL completa.

- Frontend
  - UI/UX y consumo de la API.

## Legacy notes

- Código viejo (legacy) incluye:
  - Scripts ad-hoc para migraciones que se ejecutan manualmente.
  - Lógica de transformación duplicada entre `scripts/` y `services/normalizer/`.

Plan de mitigación: extraer lógica común, añadir tests de integración para ETL y programar limpieza de cronjobs en los próximos sprints.

## Observaciones

- Separación clara entre fetch y normalización facilita reintentos y idempotencia.
- Priorizar creación de tests E2E para pipeline de datos antes de eliminar código legacy.