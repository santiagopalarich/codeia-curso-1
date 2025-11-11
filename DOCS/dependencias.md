# Política de dependencias y version bump

Normas para gestionar dependencias (librerías, paquetes, runtime) en el proyecto.

## Principios

- Preferir versiones semver y usar lockfile (package-lock.json, poetry.lock, Pipfile.lock, etc.).
- Separar dependencias de producción y de desarrollo (devDependencies).
- Revisar y aprobar updates en PRs automatizados (Dependabot, Renovate).

## Estrategia de versionado de dependencias

- Parches automáticos: aplicar `patch` automáticamente tras tests exitosos.
- Actualizaciones menores: agrupar y revisar en una PR por paquete o por área funcional.
- Actualizaciones mayores: revisar breaking changes, ejecutar pruebas E2E y coordinar release si hay riesgo.

## Proceso de bump

1. Ejecutar tests locales y en CI.
2. Si la actualización es segura (patch/minor), merge y liberar un `patch` o `minor` según impacto.
3. Para cambios mayores, abrir PR con notas de qué cambiará y plan de rollback.

## Seguridad

- Patches de seguridad tienen prioridad alta. Aplicar y desplegar lo antes posible.
- Mantener dependencia de tiempo de ejecución (p. ej. Node, Python) en `engines`/`runtime.txt`.
- Auditar dependencias periódicamente (monthly).

## Herramientas recomendadas

- Dependabot / Renovate para PRs automatizados
- Snyk o similar para escaneo de vulnerabilidades

## Notas operativas

- No mezclar actualizaciones de dependencias con cambios funcionales en la misma PR.
- Documentar cualquier cambio de contrato (por ejemplo: cambio en API de librería) en `DOCS/` y en la PR.