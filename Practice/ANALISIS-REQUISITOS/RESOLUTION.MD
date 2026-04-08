# RESOLVER-PROMPT.md — Prompt Maestro de Resolución y Roadmap

> **Propósito:** Este es el prompt para generar RESOLVER-GAPS.md y ROADMAP.md.
> Se usa ÚNICAMENTE después de que GAPS-SDLC.md está **firmado**.
> Convierte gaps en decisiones de resolución y luego en bloques de implementación ordenados.
>
> **La diferencia entre este prompt y los anteriores:**
> Los dos anteriores DESCRIBEN (qué quiere el cliente, qué falta).
> Este DECIDE (cómo se resuelve, en qué orden, quién lo hace).
> Las decisiones de este documento son las que el agente de implementación ejecuta.
> Una vez firmado, el agente no toma decisiones de diseño — solo implementa.

---

## Cuándo usar este archivo

```
GAPS-SDLC.md firmado → Parte A (RESOLVER-GAPS) → Parte B (ROADMAP)
                                ↓
                    Gate HITL de decisiones
                                ↓
                    Agente de implementación ejecuta
```

---

## Parte A — Prompt de Resolución de Gaps

```
GAPS-SDLC.md está firmado. Ahora generás RESOLVER-GAPS.md.

Para cada gap, producís una decisión de resolución. El formato es:

---

GAP-[ID] — [nombre corto del gap]
Severidad: [🔴/🟠/🟡/⚪]
Fuente requirements: [ID en CLIENT-REQUIREMENTS.md]

Resolución técnica:
  [Qué se construye o cambia para resolver este gap.
   Específico. Nombra archivos, tablas, componentes, campos.
   No "mejorar el schema" — sino "agregar campo parentId: string | null a tabla tags".]

Patrón de diseño (si aplica):
  [Si la resolución requiere un patrón, nombrarlo. Si no, omitir esta sección.]

Dependencias:
  [IDs de gaps que deben resolverse antes. Si ninguna, escribir "ninguna".]

Validación:
  [Cómo se verifica que el gap está resuelto.
   Debe ser un criterio binario: pasa o no pasa.
   Ejemplo: "useAssets() en QuickEntry y EditEntryDialog devuelven el mismo array"
   No: "el código se ve bien" — eso no es verificable.]

Decidido por: [dev / cliente / pendiente-cliente]
  Si es "pendiente-cliente": qué pregunta específica necesita respuesta antes de resolver

---

REGLAS DEL DOCUMENTO:

R1 — Cada gap tiene exactamente una resolución. No "opción A o B" — una sola decisión.
     Si hay opciones válidas, el dev elige y documenta la elección.

R2 — La resolución nombra artefactos concretos.
     No "refactorizar el código de detección" — sino "crear TrackerTypeRegistry.ts
     con enum TrackerType y eliminar los 4 string matchings en TrackerDetailView,
     WidgetCard, QuickEntry y entry-config.ts"

R3 — Las dependencias son explícitas.
     Si GAP-40 debe resolverse antes que GAP-10, eso se documenta.
     El agente de implementación no deduce el orden — lo lee de aquí.

R4 — La validación es binaria.
     "El test pasa" / "el campo existe en la DB" / "el flujo completo funciona"
     No "el código está limpio" / "parece mejor" / "debería funcionar"

R5 — Gaps de deuda técnica (TECH-DEBT.md) pueden tener resoluciones aquí
     si tienen impacto en los gaps del cliente. Si son puramente internos,
     se resuelven en un bloque separado del roadmap llamado "BLOQUE TECH-DEBT".

---

ORDEN DE PROCESAMIENTO:

1. Primero los 🔴 críticos — los que bloquean implementación
2. Luego los 🟠 altos — los que comprometen features del cliente
3. Luego los 🟡 medios — los que generan deuda creciente
4. Al final los ⚪ backburner — solo si el cliente los activó

CRITERIO DE COMPLETITUD:

El documento está completo cuando cada gap de GAPS-SDLC.md tiene una resolución
con estado "decidido por: dev" o "decidido por: cliente".
Los "pendiente-cliente" bloquean el roadmap hasta tener respuesta.
```

---

## Gate HITL — Revisión de Decisiones

Antes de generar el roadmap, revisás las resoluciones vos mismo.
Este es el momento más importante del proceso — es donde ejercés criterio técnico real.

```
□ ¿Alguna resolución nombra un archivo o componente que no existe todavía
  y que debería existir antes de que otra resolución funcione?
  → Verificar que las dependencias estén correctas

□ ¿Alguna resolución propone cambiar algo que podría romper features
  que ya funcionan?
  → Agregar nota "⚠️ breaking change — necesita migración de datos / test de regresión"

□ ¿Alguna resolución dice "refactorizar" sin nombrar exactamente qué cambia?
  → Hacerla más concreta antes de pasar al roadmap

□ ¿Hay resoluciones que la IA generó para gaps de TECH-DEBT.md
  (deuda técnica sin fuente en requirements) mezcladas con las del cliente?
  → Moverlas al bloque TECH-DEBT del roadmap, no al flujo principal

□ ¿Hay "pendiente-cliente" que vos en realidad ya podés decidir?
  → Cerrarlo como "decidido por: dev" con tu justificación
```

---

## Parte B — Prompt de Generación de Roadmap

```
RESOLVER-GAPS.md está firmado. Ahora generás ROADMAP.md.

El roadmap convierte las resoluciones en bloques de implementación ordenados.
El orden respeta las dependencias de RESOLVER-GAPS.md.

---

ESTRUCTURA DE UN BLOQUE:

## BLOQUE [número] — [nombre descriptivo]

Quién lo implementa: [agente de implementación — OpenCode / Copilot / etc.]
Lee antes de empezar: [lista de docs SDLC relevantes para este bloque]

Gaps que resuelve:
  - GAP-[ID] — [nombre corto]
  - GAP-[ID] — [nombre corto]

Qué se implementa:
  [Lista concreta de artefactos. Un ítem por artefacto.
   Ejemplo:
   → Crear TrackerTypeRegistry.ts con enum TrackerType
   → Eliminar string matching en TrackerDetailView.tsx líneas 45-89
   → Actualizar WidgetCard.tsx para usar TrackerTypeRegistry
   → Agregar campo parentId: string | null a tabla tags en schema.ts
   → Migración 002_add_tag_parent.sql]

Validación del bloque (criterios de Done):
  [Lista de criterios binarios. El bloque está done cuando TODOS pasan.
   → TrackerTypeRegistry.ts importable desde los 4 archivos afectados
   → npm run build sin errores TypeScript
   → Test: cambiar tracker de "dumbbell" a TrackerType.EXERCISE pasa
   → Migración corre sin errores en DB limpia]

¿Necesita aprobación HITL antes de continuar al siguiente bloque?
  [SÍ — cuando el bloque tiene breaking changes, migraciones de datos, o decisiones de UX
   NO — cuando es puramente técnico y no toca datos del usuario]

---

REGLAS DEL ROADMAP:

R1 — Un bloque = un conjunto coherente de cambios relacionados.
     No mezcles "arreglar el store bug" con "implementar el tag system" en el mismo bloque.

R2 — Los bloques que tienen dependencias van en el orden correcto.
     Si BLOQUE 3 depende de BLOQUE 1, BLOQUE 1 va primero.

R3 — El primer bloque siempre es el que desbloquea más gaps simultáneamente.
     Maximizar el desbloqueamiento temprano.

R4 — Los breaking changes tienen su propio bloque con HITL.
     No se mezclan con features nuevas.

R5 — El BLOQUE TECH-DEBT va al final, después de todos los gaps del cliente.
     No retrasa features por deuda técnica interna.

R6 — Cada bloque tiene una validación. Si no se puede validar, no se puede implementar.

---

BLOQUES ESPECIALES:

BLOQUE 0 — Pre-condiciones
  Para proyectos con código existente: qué debe estar estable antes de empezar.
  Tests que deben pasar, migraciones pendientes que se resuelven primero.

BLOQUE TECH-DEBT
  Siempre al final. Contiene resoluciones de TECH-DEBT.md que el dev decidió incluir.
  Opcional — el cliente no lo pidió, pero el dev lo considera necesario para escalabilidad.

BLOQUE PENDIENTE-CLIENTE
  Si hay resoluciones "pendiente-cliente" sin respuesta, van en este bloque final.
  Se implementa solo cuando el cliente responde.

---

CRITERIO DE COMPLETITUD DEL ROADMAP:

Cada gap de GAPS-SDLC.md con estado "decidido" debe aparecer en algún bloque.
Cuando la cobertura es 100% → el roadmap está completo.
```

---

## Sección 3 — Template de RESOLVER-GAPS.md

Cuando le des el prompt a la IA, pedile que use exactamente esta estructura:

```markdown
# RESOLVER-GAPS.md — Resoluciones del Proyecto [nombre]

> Generado desde: GAPS-SDLC.md (firmado [fecha])
> Fuente de verdad para: ROADMAP.md
> Una vez firmado: el agente no toma decisiones de diseño, solo implementa

---

## Resoluciones — Críticos 🔴

[gaps críticos con su resolución]

## Resoluciones — Altos 🟠

[gaps altos con su resolución]

## Resoluciones — Medios 🟡

[gaps medios con su resolución]

## Pendiente de Cliente ⏸

[lista de gaps con "decidido por: pendiente-cliente"]
[pregunta específica que necesita respuesta]

---

## Firma de Completitud

Fecha: [YYYY-MM-DD]
Gaps resueltos (dev): [N]
Gaps resueltos (cliente): [N]
Gaps pendientes cliente: [N]
Gate HITL: completado
Firmado por: Dani (Panda)
```

---

## Sección 4 — Template de ROADMAP.md

```markdown
# ROADMAP.md — Plan de Implementación [nombre]

> Generado desde: RESOLVER-GAPS.md (firmado [fecha])
> Quién lo ejecuta: [agente de implementación]
> Orden de ejecución: BLOQUE 0 → BLOQUE 1 → ... → BLOQUE TECH-DEBT

---

## Archivos de referencia SDLC

[lista de docs SDLC que el agente debe leer antes de empezar]

---

## BLOQUE 0 — Pre-condiciones

[solo si hay código existente]

## BLOQUE 1 — [nombre]

[estructura completa según el prompt arriba]

[... bloques siguientes ...]

## BLOQUE TECH-DEBT — [nombre]

[opcional — deuda técnica interna]

## BLOQUE PENDIENTE-CLIENTE

[bloqueado hasta respuesta del cliente]

---

## Firma de Completitud

Fecha: [YYYY-MM-DD]
Bloques: [N total, N con HITL, N sin HITL]
Cobertura de gaps: [N/N — 100%]
Firmado por: Dani (Panda)
```

---

## Sección 5 — El ciclo completo en una imagen

```
INPUT CRUDO DEL CLIENTE
        ↓
[EXTRACTION-PROMPT.md]
        ↓
CLIENT-REQUIREMENTS.md (draft)
        ↓
[HITL: scoring + gate]
        ↓
CLIENT-REQUIREMENTS.md (FIRMADO)
        ↓
[GAPS-PROMPT.md]
        ↓
GAPS-SDLC.md + TECH-DEBT.md (draft)
        ↓
[HITL: re-escalado + separación deuda]
        ↓
GAPS-SDLC.md (FIRMADO)
        ↓
[RESOLVER-PROMPT.md — Parte A]
        ↓
RESOLVER-GAPS.md (draft)
        ↓
[HITL: revisión de decisiones]
        ↓
RESOLVER-GAPS.md (FIRMADO)
        ↓
[RESOLVER-PROMPT.md — Parte B]
        ↓
ROADMAP.md (firmado)
        ↓
AGENTE DE IMPLEMENTACIÓN EJECUTA
```

El loop infinito no existe porque cada documento tiene:
- Criterio de completitud medible (no "sentir que está completo")
- Un HITL real antes de pasar a la siguiente fase
- Una firma que congela el documento

Si la IA "encuentra algo nuevo" después de la firma → no entra al doc actual.
Va a la siguiente versión del documento, con fecha y trazabilidad.

---

*Este archivo vive en: /SDLC/ANALISIS-REQUISITOS/RESOLVER-PROMPT.md*
*Versión: 1.0*