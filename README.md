# Observabilidad de datos de la API de Series de Tiempo de Argentina

Proyecto de observabilidad de datos (*data observability*) sobre los datos abiertos de series de tiempo de Argentina. Su objetivo es medir cuán fresca, completa e íntegra está cada serie de la [API de Series de Tiempo de datos.gob.ar](https://apis.datos.gob.ar/series/api), qué tan confiable es la bandera que la declara "actualizada" y cuánto tarda un dato en propagarse de la distribución a la API y al dump.

> **Estado:** etapa inicial (2026-11-16). Hay alcance y backlog; todavía no hay pipeline ni CI. Los resultados de abajo son observaciones puntuales sobre archivos descargados, no mediciones continuas. El trabajo pendiente está en [`docs/backlog.md`](docs/backlog.md).

## Pregunta rectora

> ¿Cuán fresca, completa e íntegra es una serie de los datos abiertos argentinos, qué tan confiable es la bandera que dice que está "actualizada", y cuánto tarda cada eslabón (distribución → API → dump) en propagar un dato?

## Resultados hasta ahora

| Hallazgo | Evidencia | Limitación |
|---|---|---|
| Los generadores del dump de metadatos completan cinco columnas con el campo equivocado | CSV descargado el 2026-11-11: `dataset_responsable == dataset_titulo` en 34.599 de 34.599 filas. SQLite (fecha interna 2019-06-19): `consultas_30_dias > consultas_90_dias` en 18.034 de 21.209 filas | Corrección propuesta en la PR [#693](https://github.com/datosgobar/series-tiempo-ar-api/pull/693), sin mergear; los dumps descargados mantienen el error hasta que se regeneren |
| El SQLite descargado el 2026-11-11 tiene fecha interna 2019-06-19 y no contiene los valores que indica la documentación | El archivo dentro del ZIP tiene fecha 2019-06-19 00:41. La [documentación de los dumps](https://github.com/datosgobar/series-tiempo-ar-api/blob/bc68937a/src/content/docs/dumps.md) indica que el SQLite contiene los paquetes como tablas; en el archivo, `valores` y `fuentes` tienen 0 filas y `metadatos` 21.209 | Una sola descarga. La URL documentada es `https://apis.datos.gob.ar/series/api/dump/series-tiempo-sqlite.zip`; falta confirmar que la copia proviene de ella (T1.1.0) |
| La regla `30 ≤ 90 ≤ 180 ≤ total` de los contadores de consultas no se cumple en 2 de 34.599 filas del CSV | Medición del 2026-11-16 sobre el CSV descargado el 2026-11-11 | Script pendiente de incorporar al repositorio (H0.4); origen de las 2 filas sin investigar (TH.9); no es el error de las columnas cruzadas |

## Limitaciones

- No hay medición continua: los resultados provienen de archivos descargados el 2026-11-11.
- La fecha y la cadencia de generación del dump son desconocidas.
- Los términos de uso y las licencias de las fuentes están pendientes de confirmar (tarea H0.5 del backlog).

## Documentación

| Documento | Contenido |
|---|---|
| [`docs/alcance.md`](docs/alcance.md) | Qué entra y qué no, con el porqué |
| [`docs/backlog.md`](docs/backlog.md) | Épicas, historias, tareas y criterios de aceptación |

## Licencia

Código bajo licencia MIT ([`LICENSE`](LICENSE)). Los datos de terceros no están incluidos y tienen sus propias condiciones.

## Propósito

Es un proyecto personal de aprendizaje y portfolio, sin fines comerciales: no se vende ni se ofrece como producto o servicio. Los datos pertenecen a sus fuentes; sus licencias y términos de uso se documentarán en `docs/datos_y_licencias.md` (H0.5) antes de redistribuir cualquier dato derivado.

---

## Summary (English)

Data observability project for Argentina's open time series ([datos.gob.ar Time Series API](https://apis.datos.gob.ar/series/api)). It aims to measure how fresh, complete and consistent each series is, how reliable its "updated" flag is, and how long a data point takes to propagate from the source distribution to the API and to the published dump. Early stage: scope and backlog only. First result: five crossed columns in the metadata dump generators, reported upstream ([#692](https://github.com/datosgobar/series-tiempo-ar-api/issues/692)) with a proposed fix in [#693](https://github.com/datosgobar/series-tiempo-ar-api/pull/693), not yet merged. Code under the MIT license. Non-commercial portfolio project.
