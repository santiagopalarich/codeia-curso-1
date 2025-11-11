# Política de versionado y releases

Define la política de versionado (SemVer) y cómo generar release notes.

## SemVer (3 números)

Formato: `MAJOR.MINOR.PATCH`

- MAJOR: cambios incompatibles (breaking changes)
- MINOR: nuevas funcionalidades compatibles hacia atrás
- PATCH: correcciones y cambios menores

Ejemplos:
- `1.0.0` -> primera release
- `1.1.0` -> nueva feature compatible
- `1.1.1` -> bugfix

## Tags

- Taggear releases en el repositorio con el prefijo `v` (ej.: `v1.2.3`).
- Cada tag debe apuntar a un commit en `main` que represente el estado de producción.

## Release notes

- Generar notas de release automáticamente desde los commits (conventional commits) y revisarlas manualmente.
- Template mínimo de release notes:
  - Title: `vX.Y.Z`
  - Date: YYYY-MM-DD
  - Highlights: lista breve de cambios importantes
  - Full changelog: sección generada automáticamente

## Proceso de release (sugerido)

1. Mergear PRs a `main` (con cambios listos para release).
2. Ejecutar CI que:
   - Corre tests
   - Genera changelog parcial
3. Crear tag `vX.Y.Z` y push al repo.
4. CI crea artefactos (builds, contenedores) y publica release draft.
5. Publicar release y desplegar.

## Notas sobre compatibilidad

- Marcar `BREAKING CHANGE` en commits que justifiquen un bump de MAJOR.
- Mantener retro-compatibilidad de la API pública cuando sea posible; documentar deprecations.