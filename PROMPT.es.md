# Prompt de arranque

Copiá el bloque de abajo y pegáselo a tu asistente de IA (Claude, ChatGPT, Cursor, o cualquier otro) al
empezar un proyecto nuevo, o cuando quieras que arranque a usar el patrón Shardlog. Son instrucciones en
texto plano — sin sintaxis específica de ninguna herramienta, funciona en cualquier lado. Explicación
completa del patrón: [`README.es.md`](README.es.md).

---

```
Desde ahora, organizá la memoria/historial de este proyecto con el patrón "Shardlog" en vez de un solo
archivo de log que crece sin parar. Especificación completa: https://github.com/ovazapadev/shardlog-indexedlog

REGLAS

1. Estructura: una carpeta raíz `bitacora/` (o el nombre que confirmemos juntos), con una subcarpeta por
   módulo/dominio de este proyecto. Dentro de cada carpeta de módulo:
   - `_index.md` — una línea por cada entrada de fecha que exista en ese módulo (fecha + resumen de una
     frase).
   - `<YYYY-MM-DD>.md` — un archivo por cada día de trabajo en ese módulo, con el detalle completo.
   - Una subcarpeta `<capa>/` SOLO si ese módulo realmente tiene contenido distinto según el rol/perfil que
     lo usa (ej. el mismo módulo significa algo estructuralmente distinto para dos tipos de usuario
     distintos). No crear capas "por las dudas" — solo cuando aparezca un conflicto real.
   También mantené un `bitacora/_index.md` raíz con todos los módulos que tienen actividad, una línea c/u.

2. Separar changelog de diseño. Esta estructura es SOLO para entradas de changelog — qué pasó, cuándo, qué
   archivos/commits se tocaron, qué se rompió y cómo se arregló. Cualquier cosa que sea una DECISIÓN DE
   DISEÑO (por qué algo funciona como funciona, qué tradeoffs se consideraron, arquitectura) va en su propio
   documento canónico por tema, donde este proyecto ya guarde ese tipo de documentación. Una entrada de
   changelog que cierra un diseño solo enlaza a ese documento — nunca copia el diseño adentro del changelog.

3. Antes de crear una carpeta de módulo nueva, pedime que confirme la lista de módulos, o inferila de la
   estructura ya existente del proyecto si no hay ambigüedad — no inventes categorías innecesarias.

4. Nunca borrar ni reescribir en bloque un archivo de log único ya existente para adoptar este patrón. Si ya
   hay uno, migrá su contenido a esta estructura preservando cada dato concreto (nombres de archivo, comandos
   corridos, decisiones, bugs y arreglos) — congelá el original como respaldo de solo lectura, no lo borres
   hasta que confirme que la migración quedó completa y correcta.

5. Acordemos una palabra/comando disparador corto (te voy a decir cuál quiero usar, ej. "*bitacora" o
   "/log") que signifique: "escribí las entradas de hoy ahora, de todos los módulos que tuvieron actividad
   esta sesión, sin volver a preguntarme qué incluir".

6. Cuando te pregunte cosas como "qué hiciste ayer/esta semana [en el módulo X]", respondé usando solo el/los
   `_index.md` relevante(s) — filtrado por fecha — y abrí el archivo de detalle de un día puntual solo si te
   pido más profundidad. Nunca releas el log de archivo único viejo ni módulos no relacionados para responder
   esto.

7. Si un nombre de módulo puede significar cosas genuinamente distintas según quién pregunta, preguntame cuál
   quiero decir antes de asumir — no adivines en silencio ni mezcles historial incompatible bajo una misma
   carpeta.

Confirmá que entendiste esto, pedime la lista inicial de módulos (o proponé una según el proyecto), y
preguntame qué palabra disparadora quiero usar antes de crear nada.
```

---

## Notas

- Este prompt es intencionalmente extenso y explícito — se pega una vez por proyecto, no una vez por sesión.
  La mayoría de los asistentes lo van a recordar el resto de esa conversación/sesión; para memoria
  persistente entre sesiones, guardalo donde viva el archivo de memoria/instrucciones de tu asistente (ej.
  un `CLAUDE.md`, un system prompt, un archivo de reglas del proyecto).
- La palabra disparadora (regla 5) es la pieza que hace esto sostenible día a día — se acuerda una vez, se
  reusa para siempre, sin tener que reexplicar qué significa "actualizá el log" cada vez.
- 🇬🇧 English version: [`PROMPT.md`](PROMPT.md)
