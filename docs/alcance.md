# Alcance

> **Estado:** v0.1 (2026-11-16). Decisiones abiertas en [`backlog.md`](backlog.md#decisiones-que-bloquean-historias). Convención: ✅ verificado · ⚠️ no verificado.

## Pregunta rectora

> ¿Cuán fresca, completa e íntegra es una serie de los datos abiertos argentinos, qué tan confiable es la bandera que dice que está "actualizada", y cuánto tarda cada eslabón (distribución → API → dump) en propagar un dato?

Cada componente existe para responder alguna parte de esta pregunta. Lo que no la responde no entra.

## Fuentes

| Fuente | Rol | Por qué |
|---|---|---|
| API de Series de Tiempo de datos.gob.ar y su dump de metadatos | Eje | El dump descargado el 2026-11-11 describe 34.599 series ✅; el dump, la documentación de la API y el código fuente son públicos |
| APIs del BCRA (Estadísticas Monetarias y Cambiarias; versiones ⚠️ por confirmar en su documentación) | Segunda fuente | Es la fuente primaria de series del BCRA que en el catálogo de Series tienen su último dato en 2021 o antes ⚠️ (cantidad a recalcular en TH.4) |

## Dentro del alcance

| # | Componente | Por qué | Qué lo invalidaría |
|---|---|---|---|
| 1 | Archivado diario del dump y de series centinela | Sin una secuencia de snapshots no se pueden medir la latencia ni la evolución de la frescura | Que el dump deje de publicarse: la medición pasa a depender de las centinelas |
| 2 | Capas bronze → silver → gold | Permite reprocesar sin volver a consultar la fuente y trazar cada resultado a un archivo | — |
| 3 | KPIs de frescura, completitud, confiabilidad de la bandera y latencia | Responden directamente la pregunta rectora | Si la bandera oficial resulta confiable, el KPI de confiabilidad se limita a documentarlo |
| 4 | Calidad como código y detección de drift | La regla `dataset_responsable ≠ dataset_titulo` habría detectado el error reportado en [#692](https://github.com/datosgobar/series-tiempo-ar-api/issues/692) | — |
| 5 | Integración BCRA | Mapa de migración de series sin datos recientes a variables vigentes | Que no haya correspondencia entre ambos catálogos (TH.4) |
| 6 | API de lectura y dashboard | Cada vista apoya una decisión de un usuario identificado | — |
| 7 | Regresión lineal del rezago | Análisis observacional con errores estándar robustos por clúster y validación por grupos | Que el análisis exploratorio no muestre una variable de respuesta razonable |
| 8 | Servidor MCP de solo lectura con evaluación | Consultas sobre el estado de una serie cuyas cifras salen de SQL | La falta de un conjunto de evaluación: sin él, no se publica |
| 9 | Despliegue y operación | La operación incluye backups, observabilidad y runbook, además del despliegue | Un costo por encima del tope (D2) |
| 10 | Contribución upstream | Cierra el ciclo: detectar, reproducir y proponer la corrección | — |

## Fase 2 (se decide en M2)

- Ingesta de todos los valores (D1), solo si el volumen cabe en la infraestructura elegida.
- Caso aplicado de traspaso del tipo de cambio a los precios, como uso de la plataforma.

## Fuera del alcance

| Qué | Por qué |
|---|---|
| Central de Deudores del BCRA | Contiene datos personales de situación crediticia y no aporta a la pregunta |
| Streaming | ⚠️ Las series se actualizan con frecuencia diaria o menor (a verificar con los valores de `indice_tiempo_frecuencia`); el procesamiento por lotes es suficiente |
| Kubernetes | La carga prevista no lo justifica |
| Scraping del portal HTML | Se usan solo APIs y archivos publicados para consumo programático. Los términos de uso y el `robots.txt` del portal están pendientes de verificar (TH.3, TH.8) |
| Afirmaciones causales y pronósticos | El diseño observacional no los sostiene |
| Copia espejo de todos los datos | No responde a la pregunta rectora |

## Novedad

Afirmación admitida: no se encontró un proyecto que mida la frescura por valores y la latencia por etapa de esta API. No se afirma que no exista. Antecedentes a revisar: `pydatajson`, Open Data Portal Watch y las series "Indicadores de la Red de Datos Abiertos" del propio catálogo (TH.6).

## Upstream

Los generadores del dump de metadatos completan cinco columnas con el campo equivocado; el error está verificado contra los dumps descargados el 2026-11-11. Issue [#692](https://github.com/datosgobar/series-tiempo-ar-api/issues/692) y PR [#693](https://github.com/datosgobar/series-tiempo-ar-api/pull/693), abiertos el 2026-11-16.
