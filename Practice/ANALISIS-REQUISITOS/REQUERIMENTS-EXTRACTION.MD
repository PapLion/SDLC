# EXTRACTION-PROMPT.md — Prompt Maestro de Extracción de Requisitos

> **Propósito:** Este es el prompt que le pasás a la IA junto con el input crudo del cliente.
> Se usa UNA vez, al inicio del proyecto, para convertir caos en estructura.
> No se itera sin razón — tiene criterio de completitud propio.
>
> **Cuándo usarlo:** Cuando tenés chat, notas de voz, Notion, imágenes, o cualquier
> combinación de inputs del cliente y necesitás convertirlo en CLIENT-REQUIREMENTS.md.
>
> **Output esperado:** Un draft de CLIENT-REQUIREMENTS.md + SCORING-SHEET.md completados.

---

## Cómo usar este archivo

1. Copiá el prompt de la Sección 1 y pegalo en tu sesión con la IA
2. Adjuntá TODOS los inputs crudos del cliente (chat completo, imágenes, Notion, etc.)
3. La IA produce el draft — **no lo aceptés sin pasar por el Gate HITL** (Sección 3)
4. Completás el scoring vos mismo (Sección 2)
5. Firmás el documento cuando el criterio de salida se cumple

---

## Sección 1 — Prompt de Extracción

```
Sos un extractor de requisitos. Tu trabajo es ÚNICAMENTE clasificar lo que el cliente
dijo en las 7 categorías definidas abajo.

NO inferís. NO proponés mejoras. NO hacés recomendaciones de arquitectura.
NO agregás edge cases que el cliente no mencionó.
Solo organizás, con fidelidad quirúrgica, lo que está en el input.

---

CATEGORÍAS DE EXTRACCIÓN:

RF — Requisito Funcional
  El sistema debe HACER algo. Verbos: mostrar, guardar, calcular, enviar, notificar,
  filtrar, crear, editar, eliminar, exportar.
  Criterio: "¿Esto describe una acción del sistema?" → SÍ = RF

RNF — Requisito No Funcional
  Cómo debe hacerlo: performance, seguridad, plataforma, escala, accesibilidad, idioma.
  Criterio: "¿Esto es una restricción de calidad, no una feature?" → SÍ = RNF

UX — Experiencia de Usuario
  Flujo, feel, layout, tono, preferencia visual que el cliente describió explícitamente.
  Criterio: "¿El cliente describió CÓMO se ve o siente?" → SÍ = UX

DATO — Modelo de Datos
  Campos, estructuras, relaciones, tipos de datos que el cliente mencionó.
  Criterio: "¿El cliente nombró un dato o campo específico?" → SÍ = DATO

REGLA — Regla de Negocio
  Condiciones, lógica condicional, restricciones, edge cases que el cliente especificó.
  Criterio: "¿Hay un SI / ENTONCES / EXCEPTO / SIEMPRE / NUNCA?" → SÍ = REGLA

ABIERTO — Pendiente de Respuesta
  El cliente mencionó algo pero no lo definió. Necesita respuesta antes de implementar.
  Criterio: "¿Hay ambigüedad que bloquea el diseño?" → SÍ = ABIERTO

DESCARTE — No entra al documento
  Pagos/cobros entre cliente y desarrollador, errores ya resueltos, logística personal,
  mensajes de coordinación, cosas ya confirmadas como fuera de scope.
  Criterio: "¿Esto afecta lo que se construye?" → NO = DESCARTE

---

FORMATO DE SALIDA OBLIGATORIO:

Para cada ítem extraído:
  ID | CATEGORÍA | texto literal o parafraseo fiel | fuente (mensaje N, imagen N, etc.)

Ejemplo:
  RF-01 | RF | El bot debe enviar DM al ganador con el premio | chat msg 47
  RF-02 | RF | Si el DM falla, enviar el mismo mensaje en el canal | chat msg 47
  RNF-01 | RNF | El sistema debe soportar múltiples jugadores simultáneos | chat msg 12
  UX-01 | UX | Los trackers deben mostrarse en fila horizontal, no en lista | imagen 6
  DATO-01 | DATO | Campo: value (peso en lbs, requerido) | Notion tracker Weight
  REGLA-01 | REGLA | Si bedtime < waketime, las horas se calculan cruzando medianoche | Notion Sleep
  ABIERTO-01 | ABIERTO | ¿Cuáles son los rangos de score para cada color de Mood? | Notion Mood
  DESC-01 | DESCARTE | Discusión de precio del proyecto | chat msg 3-8

Al final del documento, incluir:

CONTEO:
  RF: N ítems
  RNF: N ítems
  UX: N ítems
  DATO: N ítems
  REGLA: N ítems
  ABIERTO: N ítems (estos van a sección "Pendiente" al final de CLIENT-REQUIREMENTS)
  DESCARTE: N ítems

REGISTRO DE FUENTES PROCESADAS:
  [lista cada fuente que procesaste y cuántos ítems extraíste de ella]
  Ejemplo:
  - chat completo (msgs 1-180): 34 ítems extraídos + 12 descartados
  - imagen 1 (mockup app original): 5 ítems extraídos
  - Notion tracker discovery: 28 ítems extraídos

CRITERIO DE COMPLETITUD:
  Terminás cuando cada fuente del input tiene al menos un ítem en alguna categoría
  o está documentada como "sin ítems nuevos" en el registro.
  NO terminás por intuición. Terminás cuando el registro de fuentes está completo.

---

PROHIBICIONES ESTRICTAS:

❌ No agregues ítems que "el cliente probablemente quiso decir"
❌ No combines dos ítems distintos en uno solo para simplificar
❌ No omitas ítems por considerarlos "obvios"
❌ No hagas secciones de arquitectura, recomendaciones, ni inferences
❌ No reformules un ítem hasta cambiar su significado original

---

INSTRUCCIÓN DE INICIO:

Procesá todos los inputs adjuntos ahora.
Primero listá todas las fuentes que vas a procesar y su tipo (chat, imagen, Notion, etc.).
Luego procesá cada una en orden.
Reportá el conteo parcial cada vez que termines una fuente.
No termines hasta que el registro de fuentes esté completo.
```

---

## Sección 2 — Scoring Sheet

Después de que la IA genera el draft, completás esto vos. Tarda 15-20 minutos pero
es el único HITL real del proceso. Sin este paso el documento no está firmado.

**Instrucciones:**
- Leé cada RF, RNF, UX y DATO del draft
- Marcá el estado según los criterios de cada categoría
- Los ABIERTO sin resolver NO bloquean — van a la sección "Pendiente de cliente"
- El doc está listo cuando 0 RF tienen estado "incompleto" y 0 DATO tienen estado "incompleto"

### Scoring de RF (Requisitos Funcionales)

Para cada RF, respondé las 3 preguntas. Si las 3 son "sí" → completo.

| ID | RF | ¿Quién lo activa? | ¿Qué produce? | ¿Bajo qué condición? | Estado |
|----|----|-------------------|---------------|----------------------|--------|
| RF-01 | [texto] | sí/no | sí/no | sí/no | completo / incompleto |

Acciones cuando está incompleto:
- ¿La respuesta está en otro mensaje del chat que la IA no capturó? → Agregala como ítem nuevo
- ¿Necesita respuesta del cliente? → Moverlo a ABIERTO con la pregunta específica
- ¿Es una decisión de diseño tuya como dev? → Marcalo como "decisión dev" y anotá la decisión

### Scoring de DATO (Modelo de Datos)

Para cada DATO, respondé las 3 preguntas. Si las 3 son "sí" → completo.

| ID | DATO | ¿Tiene nombre? | ¿Tiene tipo? | ¿Tiene origen? | Estado |
|----|------|----------------|--------------|----------------|--------|
| DATO-01 | [texto] | sí/no | sí/no | sí/no | completo / incompleto |

### Scoring de RNF y UX

Para RNF: ¿tiene criterio medible? (sí/no) → completo si sí
Para UX: ¿describe algo concreto, no una preferencia vaga? (sí/no) → completo si sí

### Scoring de ABIERTO

| ID | Pregunta | ¿Ya hay respuesta en el input? | Acción |
|----|----------|-------------------------------|--------|
| ABIERTO-01 | [pregunta] | sí (mensaje N) / no | cerrar con respuesta / mantener abierto |

---

## Sección 3 — Gate HITL: Lo que la IA nunca puede saber

Antes de firmar el documento, respondé estas preguntas vos mismo.
Son las cosas que requieren contexto humano que no está en el input escrito.

```
□ ¿Hay algo que el cliente dijo en tono de "obviamente" que la IA podría haber ignorado
  por parecer menor? (ej: "obvio que tiene que ser rápido", "esto no puede fallar")
  → Si sí, agregarlo como RNF

□ ¿Hay algo que vos ya sabés del cliente (de proyectos anteriores o conversaciones
  informales) que nunca quedó escrito pero afecta lo que se construye?
  → Si sí, agregarlo con fuente "conocimiento previo - [fecha]"

□ ¿Hay algún ABIERTO que sabés que el cliente ya respondió informalmente
  pero nunca quedó en el chat?
  → Si sí, cerrarlo con "respondido verbalmente - [fecha]" y la decisión

□ ¿El cliente envió imágenes o mockups? ¿Las revisaste una por una buscando
  elementos que no verbalizó en texto?
  → Las imágenes son la fuente más común de gaps no capturados

□ ¿Hay algo en el input que suena a "más adelante" o "futuro" pero que en realidad
  ya está decidido que entra en este scope?
  → Moverlo de ABIERTO a RF/RNF con nota "scope confirmado"
```

---

## Sección 4 — Criterio de Firma

El documento CLIENT-REQUIREMENTS.md está **firmado** cuando:

```
□ Registro de fuentes completo — cada fuente procesada y documentada
□ 0 RF con estado "incompleto"
□ 0 DATO con estado "incompleto"
□ Gate HITL completado (las 5 preguntas respondidas)
□ Sección "Pendiente de cliente" tiene una pregunta específica por cada ABIERTO sin resolver
□ Fecha de firma anotada al final del documento
```

Una vez firmado: el documento NO se itera más.
Si el cliente da nueva información → nueva versión con fecha, no edición silenciosa.
Si la IA "encuentra algo nuevo" en una revisión posterior → solo entra si tiene fuente
en el input original. Si no tiene fuente, no es un requisito del cliente — es una opinión.

---

## Sección 5 — Template de Firma

Pegá esto al final de tu CLIENT-REQUIREMENTS.md cuando esté listo:

```
---
## Firma de Completitud

Fecha: [YYYY-MM-DD]
Fuentes procesadas: [N fuentes, lista breve]
RF completos: [N]
RNF completos: [N]
UX completos: [N]
DATO completos: [N]
REGLA completos: [N]
ABIERTO sin resolver: [N] — en sección "Pendiente de cliente"
Gate HITL: completado
Firmado por: Dani (Panda)

Este documento es la fuente de verdad para GAPS-SDLC.md.
Cualquier requisito que no aparezca aquí no existe para el proyecto.
```

---

*Este archivo vive en: /SDLC/ANALISIS-REQUISITOS/EXTRACTION-PROMPT.md*
*Versión: 1.0*