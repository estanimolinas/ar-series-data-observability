# Backlog

> **Estado:** borrador v0.1 (2026-11-16), pendiente de aprobación. Las épicas cercanas (E0, H0, E1, E10) están detalladas en historias, tareas y criterios de aceptación; las demás, solo en historias, y se detallan al acercarse su hito. Convención: ✅ verificado · ⚠️ no verificado · 🟡 dato de calendario o fuente no confirmado.

## Pregunta rectora

> ¿Cuán fresca, completa e íntegra es una serie de los datos abiertos argentinos, qué tan confiable es la bandera que dice que está "actualizada", y cuánto tarda cada eslabón (distribución → API → dump) en propagar un dato?

Criterio de admisión: una historia entra al backlog solo si contribuye a responder alguna parte de esta pregunta. Lo que no la responde va a "Fuera de alcance" o a Fase 2.

## Usuarios

| Código | Usuario | Qué necesita |
|---|---|---|
| U1 | Analista | Decidir si puede usar una serie |
| U2 | Responsable de catálogo | Priorizar qué corregir |
| U3 | Mantenedor de la API | Hallazgos reproducibles y correcciones |
| U4 | Agente de IA | Consultar el estado de una serie con respuestas verificables |
| U5 | Operador del proyecto | Saber si el propio pipeline funciona |

## Decisiones que bloquean historias

| ID | Decisión | Bloquea | Recomendación |
|---|---|---|---|
| D1 | Ingesta de todos los valores (Fase 2) | E2, E3 | Decidir en M2 con datos de H0 |
| D2 | Hosting y tope de costo mensual | E9, H1.4 | Fijar el tope antes de M1 |
| D3 | Frontend | E6 | React; Streamlit como alternativa si el plazo no alcanza |
| D4 | Acceso público a API/MCP y autenticación | E6, E8 | Solo lectura, con límite de tasa |
| D5 | Idioma del README | E11 | Español con resumen en inglés |
| D6 | Nombre del repositorio | H0.1 | Título "Observabilidad de datos de la API de Series de Tiempo de Argentina"; repositorio `ar-series-data-observability` (✅ 2026-11-16) |
| D7 | Licencia del código y de los datos derivados | H0.1, H0.5 | ✅ Código: MIT (2026-11-16). Datos derivados: según la licencia de cada fuente (TH.8) |
| D8 | Horas semanales reales | Hoja de ruta | — |
| D9 | Dónde corre y guarda el archivador mínimo | H1.1 | ✅ Decidido 2026-11-16: GitHub Actions; snapshots como assets de Releases y manifest en el repositorio ([ADR 0001](adr/0001-archivador-en-github-actions.md)) |
| D10 | Ejecución local | Todas | ✅ Decidido 2026-11-16: nada corre ni se guarda en la máquina local; CI, tests y builds en GitHub Actions con Docker |

---

## E0 — Fundaciones

### H0.1 Repositorio con identidad personal
*Como operador (U5), quiero que el repositorio exista con la identidad personal y sin datos ni notas privadas, para publicarlo sin exponer información que no corresponde.*

| Tarea | Estado |
|---|---|
| T0.1.1 `git init -b main` | ✅ 2026-11-16 |
| T0.1.2 `.gitignore` (notas, datos, workspace del editor) | ✅ 2026-11-16 |
| T0.1.3 `user.email` local = noreply personal | ✅ 2026-11-16 |
| T0.1.4 Primer commit (esqueleto de README y `docs/`) | ✅ 2026-11-16 |
| T0.1.5 Crear el repositorio en GitHub (D6, D7) y hacer push | ✅ 2026-11-16: [estanimolinas/ar-series-data-observability](https://github.com/estanimolinas/ar-series-data-observability), público |

**Criterios de aceptación**
- [x] `git log -1 --format='%an <%ae> | %cn <%ce>'` muestra el noreply personal como autor y como committer.
- [x] `git status --ignored` lista como ignorados `raw/`, `series-tiempo-metadatos.csv`, el prompt y el `.code-workspace`.
- [x] El repositorio remoto no contiene ningún CSV, SQLite ni ZIP (`git ls-files | grep -E '\.(csv|sqlite|zip)$'` vacío).

### H0.2 CI desde el primer commit
*Como operador (U5), quiero que cada push corra lint y tests, para que el estado del README refleje el código real.*

- T0.2.1 Proyecto Python con `uv` (`pyproject.toml`, `uv.lock`).
- T0.2.2 `Dockerfile` del entorno de desarrollo y pruebas, para que la CI y cualquier ejecución usen la misma imagen (D10).
- T0.2.3 Workflow de GitHub Actions que construye la imagen y corre `ruff check` + `pytest` dentro de ella.
- T0.2.4 Badge de CI en el README.

**Criterios de aceptación**
- [ ] El workflow pasa en `main` con al menos un test real (no un placeholder vacío).
- [ ] Una rama con un test que falla muestra el check en rojo.
- [ ] Ningún paso requiere instalar algo en la máquina local.

### H0.3 Registro de decisiones
*Como lector del portfolio, quiero ver por qué se eligió cada tecnología, para evaluar los fundamentos de cada decisión.*

- T0.3.1 Plantilla de ADR en `docs/adr/0000-plantilla.md`.
- T0.3.2 Tabla resumen en `docs/decisiones.md`, enlazada desde el README.

**Criterios de aceptación**
- [ ] Cada ADR tiene contexto, alternativas consideradas, decisión, consecuencias y qué la invalidaría.

### H0.4 Primeros artefactos bronze registrados
*Como operador (U5), quiero registrar los dumps ya descargados con su huella, para que cualquier cálculo posterior sea trazable a un archivo concreto.*

- T0.4.1 Manifest con URL de origen, fecha de descarga, tamaño, sha256 y filas para:
  - `series-tiempo-metadatos.csv`: 26.399.502 bytes ✅, sha256 `67c108d4…eaf6` ✅ (registrado el 2026-11-11), 34.599 filas ✅;
  - `series-tiempo-sqlite.zip`: 2.317.786 bytes ✅, fecha interna 2019-06-19 ✅, sha256 y URL de origen pendientes.
- T0.4.2 Incorporar al repositorio los scripts de medición usados el 2026-11-16 (conteos del CSV y del SQLite).
- T0.4.3 Script que recalcula el manifest y falla si no coincide.

**Criterios de aceptación**
- [ ] El script reproduce el sha256 y la cantidad de filas registradas.
- [ ] Cada cifra publicada en el README se reproduce con un script del repositorio.
- [ ] El conteo de filas usa un lector CSV real (`newline=""`), no `wc -l`.

### H0.5 Datos y licencias
*Como lector y como usuario de los datos derivados, quiero conocer la licencia y los términos de cada fuente, para saber qué se puede redistribuir.*

- T0.5.1 `docs/datos_y_licencias.md` con una fila por fuente: URL, licencia, términos de uso, robots.txt, límites de tasa.

**Criterios de aceptación**
- [ ] Cada dato tiene una fuente citada o figura como "pendiente". No se completa ninguno por suposición.

---

## H0 — Investigación previa (resolver antes de escribir código de aplicación)

Cada tarea se cierra con la respuesta y su evidencia en `docs/hallazgos.md`.

| Tarea | Pregunta | Estado |
|---|---|---|
| TH.1 | Fecha y cadencia de generación del dump de metadatos | ⚠️ `Last-Modified` 2026-08-25 05:39:24 GMT, con el mismo `etag`, el 2026-11-11 y el 2026-11-16; cadencia desconocida. `Last-Modified` puede ser la fecha de subida al almacenamiento y no la de generación |
| TH.2 | Cómo se calculan `serie_actualizada` y `serie_dias_no_cubiertos` | pendiente (leer `apps/management`, `meta_keys`) |
| TH.3 | Rate limits y robots.txt de la API de Series y del BCRA | pendiente |
| TH.4 | Tamaño del catálogo BCRA v4.0 y su relación con las series congeladas | pendiente |
| TH.5 | ID de la serie de IPC alimentos nacional | pendiente (candidato ⚠️ `101.1_I2AB_2016_M_26`) |
| TH.6 | Qué miden las series "Indicadores de la Red de Datos Abiertos" | pendiente (afecta la afirmación de novedad) |
| TH.7 | Contenido del SQLite publicado | ✅ 2026-11-16: `metadatos` 21.209 filas; `valores` y `fuentes` vacías; fecha interna 2019-06-19 |
| TH.8 | Licencia y términos para redistribuir datos derivados | pendiente (alimenta H0.5) |
| TH.9 | Origen de las 2 filas del CSV con `consultas_30_dias > consultas_90_dias` | pendiente (no es el error de las columnas cruzadas) |
| TH.10 | Valores de `indice_tiempo_frecuencia` presentes en el dump | pendiente (respalda la exclusión de streaming) |
| TH.11 | Por qué el SQLite no contiene valores ni fuentes, si la documentación indica que sí | pendiente; posible reporte upstream si se confirma con una descarga actual |

---

## E1 — Ingesta a bronze

### H1.1 Archivador mínimo del dump (urgente)
*Como operador (U5), quiero archivar el dump de metadatos todos los días a partir de ahora, para no perder la historia que necesitan los KPIs de latencia y frescura.*

Se adelanta a Dagster y Postgres: un script autónomo alcanza. Corre en GitHub Actions ([ADR 0001](adr/0001-archivador-en-github-actions.md)).

- T1.1.0 ✅ 2026-11-16 (resultado en el [ADR 0001](adr/0001-archivador-en-github-actions.md#verificación)). Prueba de alcance: un workflow manual (`workflow_dispatch`) que hace un `HEAD` al dump de metadatos y al SQLite (`series-tiempo-sqlite.zip`) desde un runner de GitHub y registra el código, la URL final, el tamaño, el `Last-Modified` y el tiempo de respuesta. Si no llega, se revisa el ADR antes de seguir. El tamaño del SQLite se compara con la copia local (2.317.786 bytes) para confirmar su origen.
- T1.1.1 Script `archive_dump`: GET condicional con `If-None-Match` (✅ responde `304` sin cambios, 2026-11-11), sigue redirecciones y registra la URL final, el ETag, el `Last-Modified`, el sha256 y el tamaño.
- T1.1.2 Validación antes de guardar: cuerpo no vacío, `content-type` CSV, no HTML, el encabezado tiene las 29 columnas esperadas.
- T1.1.3 Snapshot nuevo → asset de un Release nombrado por su sha256. Manifest (JSON Lines) commiteado por el workflow, con una línea por consulta, haya cambios o no.
- T1.1.4 Programación diaria con `schedule`. La marca de tiempo es la de la consulta, no la del horario programado.
- T1.1.5 Alerta si el workflow falla o si pasan más de 48 h sin una entrada en el manifest.

**Criterios de aceptación**
- [ ] Dos corridas seguidas sin cambios en el origen dejan un solo artefacto y dos entradas en el manifest (la segunda con `304`).
- [ ] Una respuesta HTML o vacía se rechaza y queda registrada como fallo, sin guardarse como artefacto.
- [ ] Tests con un servidor falso cubren `200`, `304`, redirección, HTML y cuerpo vacío.

### H1.2 Series centinela
*Como analista (U1), quiero saber cuánto tarda un dato en pasar de la distribución a la API y al dump, para saber qué tan atrasada puede estar la serie que consulto.*

- T1.2.1 Lista fija de centinelas con su justificación: por lo menos una mensual (IPC `148.3_INIVELNAL_DICI_M_26`, ✅ existe en la API) y una diaria (⚠️ a elegir).
- T1.2.2 Poller de la API y del CSV de distribución; registra la "primera vez visto" de cada nuevo `indice_tiempo`.

**Criterios de aceptación**
- [ ] Para cada centinela y etapa queda una marca de tiempo de primera aparición por período.
- [ ] Una centinela sin cambios no genera escrituras nuevas, solo una entrada de consulta.

### H1.3 Cliente HTTP con control de tasa
*Como mantenedor de la fuente (U3), quiero que el proyecto no sobrecargue los servidores públicos, para que la recolección sea aceptable.*

- T1.3.1 `User-Agent` identificable, con el contacto tomado de una variable de entorno (sin un email hardcodeado).
- T1.3.2 Límite por host (token bucket), backoff exponencial con jitter ante `429` o `5xx`, y respeto de `robots.txt`.

**Criterios de aceptación**
- [ ] Tests: ante un `429`, el cliente espera y reintenta dentro del tope; ante `robots.txt` que prohíbe, no consulta.

### H1.4 Manifest y orquestación definitivos
*Como operador (U5), quiero migrar el archivador a la orquestación elegida sin perder la historia, para tener lineage y backfill.*

- Depende de los ADR de orquestación (Dagster, ⚠️ a ratificar) y de almacén (Postgres), y de D2.
- Criterio clave: la migración importa el manifest de H1.1 sin perder ninguna entrada.

---

## E2 — Silver *(se detalla antes de M1)*

- H2.1 Contrato de la fuente para el dump: esquema, tipos y enums; las filas que no lo cumplen van a cuarentena con un código de motivo.
- H2.2 Dimensión de series con historial de cambios (SCD2) a partir de los snapshots archivados.
- H2.3 Snapshots diarios normalizados por serie.

## E3 — Gold y KPIs *(se detalla antes de M2)*

Definiciones operativas a calibrar con el análisis exploratorio; se documentan en `docs/kpis.md` antes de implementarse:
- H3.1 rezago y rezago normalizado por frecuencia, con el umbral `k` a calibrar ⚠️;
- H3.2 completitud;
- H3.3 confiabilidad de la bandera `serie_actualizada` (matriz de confusión);
- H3.4 latencia por etapa a partir de las centinelas;
- H3.5 integridad de metadatos por regla.

## E4 — Calidad como código y drift *(se detalla antes de M2)*

- H4.1 Reglas semánticas con severidad. Incluye `dataset_responsable ≠ dataset_titulo`, que habría detectado el error reportado en #692, y `30 ≤ 90 ≤ 180 ≤ total`, que en el CSV del 2026-11-11 no se cumple en 2 filas ✅.
- H4.2 Detección de drift de esquema entre snapshots.
- H4.3 Informe de hallazgos con códigos estables.

## E5 — Integración BCRA *(depende de TH.4)*

- H5.1 Mapa de migración "serie congelada → variable viva".
- H5.2 Comparación de valores entre las dos fuentes.

## E6 — API y dashboard *(depende de D3, D4)*

- H6.1 API de lectura sobre gold.
- H6.2 Dashboard con hasta 6 vistas, cada una ligada a una decisión de un usuario.

## E7 — Regresión lineal *(antes de M3)*

- H7.1 Modelo del rezago: OLS con errores robustos por clúster (dataset), Ridge con GroupKFold y un baseline solo con la frecuencia. Encuadre no causal.

## E8 — Capa de IA *(antes de M4)*

- H8.1 Servidor MCP de solo lectura, con números que salen de SQL.
- H8.2 Conjunto dorado de evaluación. Si no está listo, la capa de IA no se publica.
- H8.3 Búsqueda: línea base con Postgres FTS; vectores solo si la superan con una métrica fijada de antemano.

## E9 — Productivización *(depende de D2)*

- H9.1 Despliegue con Compose y TLS.
- H9.2 Backups y retención.
- H9.3 Observabilidad del propio pipeline y runbook.

## E10 — Pista upstream

### H10.1 Corrección del dump de metadatos ✅
*Como mantenedor (U3), quiero un reporte reproducible con su corrección probada, para poder aceptarla con poco esfuerzo.*

- ✅ Issue [#692](https://github.com/datosgobar/series-tiempo-ar-api/issues/692) y PR [#693](https://github.com/datosgobar/series-tiempo-ar-api/pull/693) (2026-11-16).
- ✅ Tests: 7/7 con la corrección y 12 fallos sin ella, en Python 3.6.15 y Django 1.11.6; `pycodestyle` sin avisos.

### H10.2 Nota de compatibilidad en la PR
*Como usuario de los dumps (U1), quiero saber qué columnas cambian de contenido, para adaptar mis lecturas.*

- T10.2.1 Comentario en #693 con la tabla de cambios y una línea propuesta para las notas de versión (borrador listo).

**Criterios de aceptación**
- [ ] El comentario está publicado en #693.

### H10.3 Seguimiento
- Responder la revisión. Si piden cambios, commits nuevos en la rama, sin reescribir el historial publicado.
- Criterio de corte: si pasan 4 semanas sin revisión, el hallazgo queda documentado en este proyecto y no se reitera el pedido.

### H10.4 Contribuciones posteriores *(después de que se resuelva #693)*
- Diccionario de columnas en la documentación de dumps.
- Issue aparte sobre `NULL` contra `0` en el SQLite.

## E11 — Empaquetado de portfolio

- H11.1 README según el estándar de documentación. El esqueleto se crea en H0.1 y se completa a medida que haya resultados.
- H11.2 Diagramas animados (SVG sin JavaScript, modo oscuro, `prefers-reduced-motion`) con su versión estática.
- H11.3 Demo y artículo.

---

## Fuera de alcance

El detalle y el porqué de cada exclusión están en [`alcance.md`](alcance.md#fuera-del-alcance): Central de Deudores del BCRA, streaming, Kubernetes, scraping del portal HTML, afirmaciones causales y pronósticos, copia espejo de todos los datos y fine-tuning de modelos.
