# GAPS-PROMPT.md — Prompt Maestro de Análisis de Gaps

> **Propósito:** Este es el prompt que le pasás a la IA para generar GAPS-SDLC.md.
> Se usa ÚNICAMENTE después de que CLIENT-REQUIREMENTS.md está **firmado**.
> Si el requirements no está firmado, el gap analysis no empieza.
>
> **Por qué existe este problema:** Sin taxonomía fija, la IA busca gaps en un espacio
> infinito — siempre encuentra algo nuevo. Este prompt cierra el espacio de búsqueda
> con una definición precisa de "gap válido" y un criterio de completitud medible.
>
> **La regla de oro:** Un gap válido tiene fuente en CLIENT-REQUIREMENTS.md.
> Si no tiene fuente en el requirements, no es un gap del proyecto — es una opinión
> del agente sobre buenas prácticas. Eso va a TECH-DEBT.md, no a GAPS-SDLC.md.

---

## Cuándo usar este archivo

```
CLIENT-REQUIREMENTS.md firmado → usar este prompt → GAPS-SDLC.md
                                                           ↓
                                                    RESOLVER-GAPS.md
```

No antes. El requirements firmado es el contrato de entrada.

---

## Sección 1 — Prompt de Análisis de Gaps

```
El CLIENT-REQUIREMENTS.md está firmado. Ahora analizás gaps entre lo que el cliente
pidió y lo que existe actualmente (código, diseño, schema, implementación).

---

DEFINICIÓN DE GAP VÁLIDO:

Un gap ES válido cuando:
  A) El cliente pidió algo (RF/RNF/UX/DATO/REGLA) que NO existe implementado
  B) Algo implementado CONTRADICE lo que el cliente pidió
  C) Un DATO que el cliente definió que el schema actual no puede soportar
  D) Un ABIERTO en requirements que necesita respuesta ANTES de poder diseñar o implementar
  E) Una inconsistencia entre dos partes del código que afecta un RF del cliente

Un gap NO ES válido cuando:
  ❌ Sería bueno agregar X (mejora no pedida)
  ❌ Esta es una mejor práctica de la industria (sin referencia en requirements)
  ❌ Este código podría ser más limpio (deuda técnica sin impacto funcional)
  ❌ Este edge case no está cubierto (si el cliente no lo mencionó)
  ❌ Yo lo implementaría diferente (preferencia de arquitectura)
  ❌ Este patrón es subóptimo (opinión de diseño)

TEST ANTES DE AGREGAR UN GAP:
  "¿A qué ítem del CLIENT-REQUIREMENTS.md corresponde este gap?"
  Si la respuesta es "a ninguno" → no es un gap, es deuda técnica → no entra aquí

---

FORMATO OBLIGATORIO DE CADA GAP:

GAP-[número] | [severidad] | [categoría]
Fuente requirements: [ID del ítem en CLIENT-REQUIREMENTS.md]
Descripción: [qué falta, qué contradice, o qué está incompleto]
Evidencia actual: [qué existe hoy — archivo, línea, o "no existe"]
Impacto: [qué no funciona o no puede construirse sin resolver esto]
Tipo: [ausente / contradice / schema-insuficiente / decisión-pendiente / inconsistencia]

---

ESCALA DE SEVERIDAD:

🔴 Crítico — bloquea implementación o rompe una feature del cliente
🟠 Alto — implementable pero con compromiso en lo que el cliente pidió
🟡 Medio — no bloquea pero genera deuda que crece con el tiempo
⚪ Backburner — el cliente lo puso en pausa explícitamente

---

CATEGORÍAS DE GAP (mismas que en requirements):

RF-GAP — Un requisito funcional del cliente no existe o está incompleto
RNF-GAP — Una restricción de calidad no se cumple
UX-GAP — El flujo o feel que el cliente describió no está implementado
DATO-GAP — El schema no soporta los datos que el cliente definió
REGLA-GAP — Una regla de negocio no está implementada
DECISIÓN-GAP — Un ABIERTO de requirements que bloquea el diseño (necesita respuesta)

---

PROCESO DE ANÁLISIS:

1. Leé el CLIENT-REQUIREMENTS.md completo
2. Por cada RF, RNF, UX, DATO y REGLA, preguntá: ¿existe esto en el código/diseño?
3. Si existe pero con diferencias → describí la diferencia exacta
4. Si no existe → gap tipo "ausente"
5. Por cada ABIERTO en requirements → gap tipo "decisión-pendiente"

Al terminar, producís:
  - Lista de gaps ordenada por severidad (🔴 primero)
  - Tabla de cobertura: cada ítem de requirements con estado cubierto/gap
  - Conteo final por categoría y severidad

---

CRITERIO DE COMPLETITUD — cuándo terminás:

La tabla de cobertura está completa cuando CADA ítem de CLIENT-REQUIREMENTS.md
(RF, RNF, UX, DATO, REGLA) tiene una de estas dos asignaciones:
  ✅ Cubierto — existe implementado y no contradice el requirements
  ⚠️ Gap — ID del gap correspondiente

Cuando la tabla de cobertura está 100% completa → el análisis terminó.
No buscás más. No hacés "una pasada más".
Si encontrás algo que no tiene fuente en requirements → lo ignorás o lo documentás
aparte como "candidato a deuda técnica — no es gap del cliente".

---

PROHIBICIONES:

❌ No agregues gaps que "el cliente probablemente necesita aunque no lo dijo"
❌ No combines dos gaps distintos en uno para simplificar
❌ No clasifiques mejoras de código como gaps funcionales
❌ No repitas gaps ya documentados con diferente redacción
❌ No hagas "una pasada más de seguridad" después de que la tabla de cobertura esté completa
```

---

## Sección 2 — Separador de Deuda Técnica

Lo que la IA encuentre que NO tiene fuente en requirements va aquí, no en GAPS-SDLC.md.
Después del gap analysis, pedile a la IA:

```
De todos los problemas que encontraste, listá por separado los que NO tienen fuente
en CLIENT-REQUIREMENTS.md. Esos son candidatos a TECH-DEBT.md, no a GAPS-SDLC.md.

Formato:
DT-[número] | [severidad] | descripción | por qué no es gap del cliente
```

Esto te da TECH-DEBT.md como subproducto natural del proceso, sin contaminar GAPS-SDLC.md.

---

## Sección 3 — Gate HITL del Gap Analysis

Antes de usar GAPS-SDLC.md para hacer RESOLVER-GAPS.md, respondé esto vos:

```
□ ¿Hay gaps que la IA marcó como "ausente" pero que en realidad están implementados
  en un archivo que no le pasaste al análisis?
  → Si sí, revisar si el contexto de código estuvo completo

□ ¿Hay ítems de requirements que la IA marcó como "cubiertos" pero que vos sabés
  que están incompletos o bugueados?
  → Si sí, crear gap manual con tu evidencia

□ ¿Hay gaps marcados como 🟡 medio que en realidad bloquean algo crítico del negocio?
  → Re-escalar a 🔴 con justificación

□ ¿Hay gaps marcados como 🔴 crítico que en realidad el cliente ya sabe que no están
  y los tiene como backburner?
  → Re-escalar a ⚪ con nota "cliente confirmó backburner"

□ La tabla de cobertura muestra los ABIERTOS del requirements como DECISIÓN-GAP.
  ¿Hay alguno que vos ya puedas resolver sin consultar al cliente?
  → Cerrarlo con tu decisión documentada antes de pasarlo a RESOLVER-GAPS.md
```

---

## Sección 4 — Criterio de Firma de GAPS-SDLC.md

```
□ Tabla de cobertura 100% completa (cada ítem de requirements tiene estado)
□ Cada gap tiene fuente en requirements
□ Deuda técnica separada en TECH-DEBT.md (no contaminó el gap doc)
□ Gate HITL completado
□ Gaps re-escalados si correspondía
□ DECISIÓN-GAP resueltos o escalados al cliente con pregunta específica
□ Fecha de generación anotada
```

---

## Sección 5 — Qué hacer con los DECISIÓN-GAP

Los DECISIÓN-GAP son ABIERTOS del requirements que bloquean diseño o implementación.
Tienen dos caminos:

**Camino A — Lo resolvés vos:**
Si es una decisión de diseño o técnica que podés tomar, la tomás, la documentás,
y el gap se cierra. Ejemplo: "¿Colores exactos de Mood? → Decisión dev: 1-3 rojo, 4-5 naranja,
6-7 amarillo, 8-10 verde. Documentado en RESOLVER-GAPS.md."

**Camino B — Necesita respuesta del cliente:**
Escribís la pregunta específica (no vaga) y se la mandás al cliente antes de continuar.
Ejemplo: "¿Cuándo decís que el exercise input no debería ser un modal, querés que sea
una página separada, un panel inline, o algo diferente?" — no "¿cómo querés el exercise?"

Regla: si hay más de 3 DECISIÓN-GAP sin respuesta del cliente, el proyecto
se pausa hasta tener esas respuestas. No se implementa sobre ambigüedad.

---

*Este archivo vive en: /SDLC/ANALISIS-REQUISITOS/GAPS-PROMPT.md*
*Versión: 1.0*