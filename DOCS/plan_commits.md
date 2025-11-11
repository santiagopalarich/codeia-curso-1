# Plan de commits y puntos de tag

Este documento define la convención de commits y cuándo aplicar tags/releases.

## Convención de commits

Se recomienda usar "Conventional Commits" (https://www.conventionalcommits.org/) con los tipos principales:

- feat: Nueva funcionalidad
- fix: Corrección de bugs
- docs: Cambios en la documentación
- style: Formato, espacios, sin cambios funcionales
- refactor: Refactor sin nueva funcionalidad ni fix
- perf: Cambios que mejoran performance
- test: Añadir o corregir tests
- chore: Tareas de mantenimiento

Para cambios que rompan compatibilidad añadir `BREAKING CHANGE: descripción` en el cuerpo del commit.

Ejemplo:
```
feat(movie): add posters cache

BREAKING CHANGE: se elimina endpoint /v1/old-poster
```

## Flujo y puntos de tag

- Branches principales:
  - `main`: código listo para producción. Cada merge a `main` que represente una release debe ir acompañado de un tag `vMAJOR.MINOR.PATCH`.
  - `develop` o feature branches: trabajo en curso.

- Releases y tags:
  - Taggear en el merge a `main` (o mediante PR de release) usando semver.
  - Convención de tag: `vX.Y.Z` (p. ej. `v1.2.0`)

- Cuándo incrementar version:
  - MAJOR (breaking changes): cambios incompatibles en API pública o modelo de datos.
  - MINOR (nuevas características): features backwards-compatible.
  - PATCH (fixes): correcciones menores y hotfixes.

## Automatización

- Usar CI para generar changelog (ej.: conventional-changelog) y crear un draft release en la plataforma de hosting (GitHub/GitLab) cuando se crea un tag.
- Asegurar que las PRs tengan convenciones de commit limpias antes de merge (pre-merge hook o CI check).

## Puntos operativos

- Docs y cambios no funcionales que no afectan API pública: suelen agruparse en releases de tipo `patch` o en un `chore` sin bump de API.
- Hotfix: crear rama `hotfix/x.y.z` -> merge a `main` y tag `vX.Y.Z`.

## Ejemplo de ciclo

1. Merge de PR con varias `feat`/`fix` a `main`.
2. CI genera CHANGELOG con resumen de commits.
3. Maintainer revisa release notes y crea tag `vA.B.C`.
4. Publicar release y desplegar.