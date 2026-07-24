# Shardlog

**Un patrón de changelog modular que mantiene el historial de un proyecto rápido de consultar — tanto para personas como para agentes de IA que programan. ejemplo Claude Code -Para proyectos grandes sin usar herramientas de Grafos , mas practico y mas agil a tu proyecto **

[![Pattern](https://img.shields.io/badge/tipo-patr%C3%B3n%20de%20ingenier%C3%ADa-blue)](#)
[![Stack](https://img.shields.io/badge/stack-agn%C3%B3stico-lightgrey)](#)
[![Status](https://img.shields.io/badge/estado-probado%20en%20producci%C3%B3n-brightgreen)](#)

Un solo archivo de bitácora (`CHANGELOG.md` o similar) que crece para siempre termina siendo el archivo más
caro de leer de todo el repositorio — cualquier pregunta tipo "¿qué hicimos la semana pasada?" cuesta leer
**todo** el historial, no solo la respuesta. Shardlog resuelve esto dividiendo el log en dos ejes —
**módulo** y **fecha** — con un índice barato en cada nivel, de modo que cualquier consulta cuesta lo que
vale la respuesta, no lo que pesa todo el historial del proyecto.

Nació para mantener eficiente la memoria de sesión de un asistente de IA en un proyecto de producción de
larga duración, pero el patrón en sí no tiene nada de específico de IA — funciona igual para una persona que
abre el repo a las 9am y pregunta "¿qué pasó en facturación esta semana?".

## Índice

- [El problema](#el-problema)
- [El patrón](#el-patr%C3%B3n)
- [Por qué funciona](#por-qu%C3%A9-funciona)
- [Comparación](#comparaci%C3%B3n)
- [Cómo arrancar](#c%C3%B3mo-arrancar)
- [Cuándo NO conviene](#cu%C3%A1ndo-no-conviene)
- [Preguntas frecuentes](#preguntas-frecuentes)

## El problema

Un único archivo de bitácora, agregado sesión tras sesión, acumula tres problemas que se refuerzan entre sí:

1. **Costo de lectura sin techo.** Para responder "¿qué cambió en el módulo X ayer?" hay que leer el archivo
   **completo**, porque no hay forma de recortarlo sin abrirlo primero.
2. **Dos temas distintos, un solo archivo.** *Qué pasó y cuándo* (changelog) se mezcla con *por qué pasó y
   cómo se diseñó* (arquitectura/decisiones) — con el tiempo terminan desincronizados o duplicados.
3. **No hay dónde desambiguar.** "El módulo de facturación" puede significar algo distinto para el equipo
   de finanzas que para el de operaciones — un log plano no tiene dónde codificar esa diferencia, así que se
   adivina en el momento de consultar, de forma inconsistente.

## El patrón

Dividir por **módulo** (y **capa/rol**, solo donde hay ambigüedad real) → después por **fecha**, con un
índice de una línea en cada nivel:

```
bitacora/
  _index.md                        ← mapa de todos los módulos, 1 línea c/u
  <modulo>/
    _index.md                      ← fechas tocadas en ese módulo, 1 línea c/u
    <YYYY-MM-DD>.md                ← detalle completo de ese día, ese módulo
    <capa>/                        ← SOLO si el módulo realmente difiere por rol/perfil
      _index.md
      <YYYY-MM-DD>.md
```

**Regla práctica para `<capa>`:** abrir una subcarpeta de capa solo cuando el mismo nombre de módulo
significaría dos cosas genuinamente distintas según quién pregunta (ej. un módulo de "facturación" que es
estructuralmente distinto para Finanzas que para Operaciones). Si no hay ambigüedad real, se salta la capa —
directo a `<modulo>/<fecha>.md`.

### Las decisiones de diseño viven aparte, a propósito

Shardlog es un **changelog** — *qué* pasó, *cuándo*. Deliberadamente no guarda *por qué* una feature funciona
como funciona, ni los tradeoffs detrás de un diseño. Eso va en un documento canónico aparte, uno por tema (un
ADR, un doc de diseño, lo que ya use el equipo). Una entrada de Shardlog que cierra un diseño solo lo enlaza:

```markdown
## 2026-07-23 — Rediseño del rate limiter, en producción
Se cerró el diseño discutido en `docs/rate-limiter-design.md`. Ver ese doc para el "por qué" — esta entrada
es solo el "salió, esto es lo que se tocó".
```

Sin duplicación, sin desincronización — una sola fuente canónica por decisión, una entrada de changelog que
apunta ahí.

## Por qué funciona

| # | Ventaja | Qué te da en la práctica |
|---|---|---|
| 1 | **El costo de lectura escala con la pregunta, no con el historial** | "¿Qué pasó en `checkout` ayer?" cuesta un índice chico, no todo el historial del proyecto — tenga 2 semanas o 2 años. |
| 2 | **Nunca se degrada** | Cada archivo de fecha es chico y de tamaño fijo. Solo el índice crece, y crece una línea por día — nunca se vuelve pesado de leer. |
| 3 | **Cero pérdida de información al reorganizar** | Migrar a este esquema mueve contenido, no lo resume con pérdida. El archivo original se conserva congelado como respaldo hasta confirmar la migración — nunca se borra a ciegas. |
| 4 | **Elimina la desincronización changelog/diseño** | Changelog y documentos de diseño están estructuralmente separados, así que hay un solo lugar para actualizar una decisión — el changelog solo enlaza, no copia. |
| 5 | **La desambiguación es estructural, no conocimiento tribal** | Cuando un nombre de módulo es realmente ambiguo entre roles, la subcarpeta de capa fuerza a preguntar "¿cuál de los dos?" en vez de mezclar historiales incompatibles en silencio. |
| 6 | **Agnóstico de stack y herramienta** | Son carpetas y Markdown. Funciona igual si el proyecto es Python, Java, Rust o una planilla de cálculo — describe cómo se organiza el CONOCIMIENTO sobre el trabajo, no el trabajo en sí. |
| 7 | **Auditable con las herramientas más simples que existen** | Sin base de datos, sin formato propietario. `ls`, `grep` y un editor de texto alcanzan para leer, verificar o revertir cualquier parte. |

### Por qué importa todavía más para agentes de IA

Los asistentes de programación basados en LLM pagan por cada token que leen, cada vez — incluyendo releer el
mismo archivo de bitácora cada vez más grande al inicio de cada sesión solo para "ponerse al día". Shardlog
convierte ese costo fijo y creciente en un costo proporcional a la pregunta real (primero el índice, recién
después el detalle de un día puntual si hace falta). La misma propiedad que hace esto agradable para una
persona repasando el historial lo hace materialmente más barato para un agente que reconstruye contexto en
cada sesión.

## Comparación

| | Bitácora única | [Keep a Changelog](https://keepachangelog.com/) | Notas diarias (Obsidian) | **Shardlog** |
|---|---|---|---|---|
| Costo de leer "¿qué pasó en X la semana pasada?" | Archivo completo | Archivo completo (por versión, no por módulo) | Navegación manual de carpetas | Un índice chico |
| Escala con la edad del proyecto | ❌ se degrada | ❌ se degrada | ⚠️ depende de cómo se organice | ✅ plano para siempre |
| Separa el *qué* del *por qué* | ❌ | ❌ (solo notas de release) | ⚠️ depende del usuario | ✅ por diseño |
| Granularidad por módulo/dominio | ❌ | ❌ (granularidad de versión) | ⚠️ depende del usuario | ✅ de primera clase |
| Dependencia de herramienta | Ninguna | Ninguna | Obsidian (dependencia blanda) | Ninguna |

Shardlog no reemplaza a Keep a Changelog (eso es sobre *releases*, esto es sobre *sesiones de trabajo*) —
combinan bien entre sí.

## Cómo arrancar

1. **Nombrar los módulos/dominios.** No hace falta tenerlos completos el día 1 — se agregan a medida que
   aparecen.
2. **Crear `bitacora/` con un `_index.md` vacío.**
3. **Definir la palabra/comando disparador.** Un comando corto y memorizable (`/log`, `*bitacora`, lo que
   encaje con el flujo) que signifique "escribí las entradas de hoy ahora, sin volver a preguntarme qué
   incluir" — se acuerda el significado una sola vez, al principio.
4. **Desde el día uno: diseño separado del changelog.** Aplicar esta separación después implica migración;
   arrancar con ella no cuesta nada.
5. **Abrir una subcarpeta de capa solo cuando un módulo realmente se vuelve ambiguo** — no anticiparla antes
   de que haga falta.

## Cuándo NO conviene

- **Proyectos chicos o de corta duración** que nunca van a acumular suficiente historial como para que un
  archivo único sea un problema real — la estructura de carpetas es un costo que no se recupera.
- **Sin herramienta de lectura selectiva.** Si quien lee este log de todos modos siempre tiene que abrir
  todo, dividir en archivos no ahorra nada — el beneficio está enteramente en no leer lo que no hace falta.

## Preguntas frecuentes

**¿Reemplaza al historial de git / mensajes de commit?**
No. Git cuenta *qué cambió en el código*. Shardlog cuenta *qué pasó en una sesión de trabajo* — decisiones,
contexto, cosas que no necesariamente produjeron un commit limpio y único. Son complementarios.

**¿Y si una entrada toca varios módulos?**
Se escribe una vez en cada carpeta de módulo a la que realmente pertenece, con el detalle relevante para ese
módulo. No forzar un solo lugar para una sesión que abarca varios temas.

**¿Se puede automatizar la actualización de los índices?**
Sí — un hook de pre-commit o un script chico que agregue la línea de resumen al `_index.md` cuando se crea un
archivo de fecha nuevo es un paso natural una vez que el patrón se siente cómodo haciéndolo a mano.

---

Migrado desde un archivo único de más de 1300 líneas sin perder un solo dato registrado — dividido en
archivos por módulo y fecha con índices en cada nivel, en una sola pasada, con el original conservado
congelado como respaldo verificado. Así es como el patrón funciona en la práctica.

🇬🇧 English version: [`README.md`](README.md)
