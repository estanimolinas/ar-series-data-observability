# ADR 0001 — Archivador del dump en GitHub Actions

- **Estado:** propuesto (2026-11-16)
- **Decisiones relacionadas:** D9, D10 en [`backlog.md`](../backlog.md)

## Contexto

La pregunta rectora necesita una historia del dump de metadatos: sin snapshots sucesivos no se puede medir latencia ni frescura en el tiempo. Los días no archivados no se pueden reconstruir después. Además, se decidió que nada corra ni se guarde en la máquina local (D10).

Hechos conocidos:
- ✅ El dump responde `304` a un GET condicional con `If-None-Match` (2026-11-11). Se puede consultar a diario sin descargarlo si no cambió.
- ✅ Pesa 26.399.502 bytes (copia del 2026-11-11).
- ⚠️ No se probó si los runners de GitHub llegan a `apis.datos.gob.ar`.

## Alternativas

| Alternativa | A favor | En contra |
|---|---|---|
| **GitHub Actions + Releases + manifest en el repositorio** | Sin infraestructura propia; sin costo en un repositorio público (⚠️ confirmar condiciones); snapshots inmutables y descargables | Límites de Releases y política de inactividad de los workflows programados por confirmar; los runners pueden no llegar a la fuente |
| GitHub Actions + artifacts | Simple | ⚠️ Retención limitada (hasta 90 días, por confirmar): no sirve para una historia |
| GitHub Actions + rama de datos en git | Todo en un lugar | El repositorio crece con cada snapshot del CSV (unos 25 MB) |
| GitHub Actions + object storage externo | No depende de los límites de almacenamiento de GitHub | Cuenta, costo y secretos antes de que haga falta |
| VM propia | Control total | Adelanta D2 (hosting y costo) solo para un cron |
| Máquina local | Inmediato | No está siempre encendida; contradice D10 |

## Decisión

El archivador corre como workflow programado de GitHub Actions. Cada snapshot nuevo se guarda como asset de un Release, nombrado por su sha256. El workflow commitea un manifest en JSON Lines con una línea por consulta (fecha, URL final, código HTTP, ETag, `Last-Modified`, sha256, tamaño).

## Consecuencias

- El repositorio tiene que ser público. Eso adelanta D6 (nombre) y D7 (licencia).
- Los snapshots quedan fuera del historial de git y el repositorio no crece por los datos.
- El manifest es la fuente de verdad de qué se consultó y cuándo. Los snapshots son reproducibles por su hash.
- Al migrar a la orquestación definitiva (H1.4), se importa el manifest y los snapshots quedan donde están.

## Qué la invalidaría

- Que los runners no lleguen a la fuente (T1.1.0).
- Que los límites de Releases, o la desactivación por inactividad de los workflows programados, impidan una historia continua.
- Que los términos de uso de la fuente no permitan redistribuir los snapshots en un repositorio público (TH.8). En ese caso, los snapshots van a un almacenamiento privado y solo se publica el manifest.
