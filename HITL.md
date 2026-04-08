# HITL.md — Human in the Loop: El Principio de Control Total

> **Propósito:** Este archivo define la filosofía y los protocolos de Human in the Loop para todo el SDLC.
> Es mi "constitución personal" de cómo trabajo con IA. El agente lo lee para entender cuándo pausar y esperar mi validación.

---

## 1. La Filosofía Central

**Tony Stark tiene la visión. Jarvis ejecuta con precisión.**

Yo soy Tony Stark. El agente es Jarvis. El agente no decide — implementa, propone, y espera mi validación antes de continuar.

El objetivo del HITL no es ralentizar el proceso. Es **multiplicar la calidad de mis decisiones**, no eliminar mi participación. La IA amplifica mi capacidad; no la reemplaza.

---

## 2. La Trampa de la "Tarea Enorme" (Por qué existe este documento)

Cuando trabajo con IA y pido funcionalidades completas sin dividirlas, pasan tres cosas malas:

**Deuda Cognitiva** — Hay partes del código que no entiendo al 100%.
**Alucinaciones de Lógica** — La IA pierde el hilo en los detalles finos.
**Ansiedad de Depuración** — Cuando algo falla, el área de búsqueda del error es demasiado grande.

La solución es siempre la misma: **divide el problema hasta que la solución sea trivial.**

Si una tarea parece difícil de explicar a la IA, es porque sigue siendo demasiado grande.

---

## 3. El Método de Feature Atómica

### A. Layout First (antes de lógica, estructura)
No se avanza a la siguiente sección si la anterior no está verificada. Aprendido de Klara (Landing Page) y NPCs en Roblox.

### B. Implementación Atómica
Cada comportamiento o feature se trata como un universo independiente:
1. Se implementa la feature
2. Se prueba
3. Se corrige con HITL constante
4. Solo cuando está "certificada" por mí, se integra al sistema global

### C. El Humano como Validador de Intención
La IA construye el artefacto, pero yo debo entender la **mecánica interna al 100%**.
Si no puedo explicar por qué una línea de código está ahí, la implementación ha fallado.

**Regla de oro:** No propongas la siguiente tarea hasta que yo confirme que la actual pasó su verificación (test verde, visual OK, o consola limpia).

---

## 4. Gates HITL por Fase del SDLC

El SDLC completo tiene 7 fases. Cada una con su gate de validación humana.

### Gate 1 — Firma de CLIENT-REQUIREMENTS.md (Fase 1)
**Cuándo:** Después de que la IA genera el draft con EXTRACTION-PROMPT.md.
**Qué hago yo:**
- Completo el Scoring Sheet (RF, DATO, RNF, UX) — tarda 15-20 min
- Respondo las 5 preguntas del Gate (imágenes, conocimiento previo, scope verbal, etc.)
- Verifico que cada fuente fue procesada

**Criterio de salida:** 0 RF incompletos + 0 DATO incompletos + Gate respondido.
**Efecto:** Documento congelado. Lo que la IA "encuentre después" no entra sin fuente nueva del cliente.

### Gate 2 — Firma de GAPS-SDLC.md (Fase 1)
**Cuándo:** Después de que la IA genera el gap analysis con GAPS-PROMPT.md.
**Qué hago yo:**
- Verifico que cada gap tiene fuente en requirements (si no → TECH-DEBT.md)
- Re-escalo gaps si es necesario (🟡 que en realidad bloquea → 🔴)
- Cierro DECISIÓN-GAP que yo pueda resolver sin consultar al cliente
- Si hay más de 3 DECISIÓN-GAP sin respuesta del cliente → pauso el proyecto

**Criterio de salida:** Tabla de cobertura 100% + deuda técnica separada + gaps re-escalados.

### Gate 3 — Firma de RESOLVER-GAPS.md (Fase 1)
**Cuándo:** Después de que la IA genera las resoluciones con RESOLVER-PROMPT.md.
**Qué hago yo:**
- Verifico que cada resolución nombra artefactos concretos (no "mejorar el código")
- Confirmo que las dependencias entre resoluciones son correctas
- Convierto "pendiente-cliente" en "decidido por dev" si puedo
- Marco breaking changes y migraciones de datos

**Criterio de salida:** Cada gap tiene decisión tomada. Sin "opción A o B" — una sola resolución por gap.

### Gate 4 — Aprobación de INFRA.md (Fase 3 — pre-implementación)
**Cuándo:** Antes de la primera línea de código del proyecto.
**Qué hago yo:**
- Verifico que la plataforma de hosting está elegida y documentada
- Confirmo que .env.example tiene todas las variables del proyecto
- Verifico que el entorno local corre (instala, migra, tests verde)
- Confirmo que GitHub Secrets están configurados

**Criterio de salida:** Checklist de INFRA.md sección 11 completo.

### Gate 5 — Aprobación por Bloque del ROADMAP (Fase 3)
**Cuándo:** Al finalizar cada bloque de implementación.
**Qué hago yo:**
- Ejecuto el checklist de validación manual del bloque
- Verifico que los criterios de Done binarios pasaron
- Apruebo o rechazo antes de que el agente empiece el siguiente bloque

**Criterio de salida:** Todos los criterios binarios del bloque en verde + validación manual OK.

**Bloques que requieren HITL obligatorio:**
- Breaking changes (cambios que rompen compatibilidad)
- Migraciones de datos en producción
- Cambios en lógica de auth o permisos
- Cualquier cambio en el schema de DB en producción

### Gate 6 — Checklist Pre-Deploy (Fase 5)
**Cuándo:** Antes de cualquier merge que vaya a producción.
**Qué hago yo:**
- Completo el checklist pre-deploy de DEPLOY.md (CI/CD + código + funcional)
- Verifico que las variables de entorno de producción están correctas
- Confirmo que el plan de rollback está documentado

**Criterio de salida:** Checklist de DEPLOY.md sección 4 completo. Sin atajos.

### Gate 7 — Evaluación de Cambios de Scope (Transversal)
**Cuándo:** Cada vez que el cliente pide algo nuevo o diferente durante el proyecto.
**Qué hago yo:**
- Clasifico el cambio (Tipo A/B/C/D) siguiendo SCOPE-MANAGEMENT.md sección 2
- Evalúo impacto en el ROADMAP activo
- Comunico al cliente el trade-off antes de aceptar

**Criterio de salida:** Cambio clasificado + impacto evaluado + decisión documentada en SCOPE-MANAGEMENT-LOG.md.

---

## 5. El Espejo del Backend: Construir "Ojos Artificiales"

En frontend el feedback es visual e instantáneo. En backend **no hay ojo humano que vea los datos viajando**. La solución es construir ojos artificiales:

| Herramienta | Función | Equivalente visual |
|-------------|---------|-------------------|
| `vitest --watch` | Corre tests cada vez que guardas | Auto-refresh del navegador |
| DB GUI (Supabase / DBeaver) | Ver datos cambiar en tiempo real | Ver al NPC moverse en el mapa |
| Logs estructurados (Pino / Winston) | Ver qué pasa dentro de las funciones | Consola Output de Roblox |

### El ciclo TDD como "Play" de Roblox
1. **Escribe el test que falla** → ese error rojo es tu NPC que no camina
2. **Escribe el código mínimo** para que pase
3. **Verde** = subidón de dopamina garantizado

**Regla:** No testees "toda la plataforma". Empieza con una sola función lógica. Un test atómico que corre en menos de 100ms cada vez que guardás replica la magia del feedback instantáneo.

---

## 6. El Problema del Loop Infinito (y cómo lo resolvemos)

El loop infinito de "revisá de nuevo, encontré algo más" ocurre porque **el modelo busca en un espacio infinito de posibles problemas**. Sin criterios formales de "completo", el proceso no termina nunca.

La solución es documentos firmados con criterios de completitud medibles:

```
Sin firma → espacio infinito → loop eterno
Con firma → espacio cerrado → proceso determinista
```

**Regla:** Después de la firma, si la IA "encuentra algo nuevo", no entra al documento actual. Va a la siguiente versión con fecha y trazabilidad. La versión firmada es inmutable.

**Criterio de cierre del SDLC como sistema:** El SDLC está completo contra el modelo de 7 fases con roles. Cualquier cosa que la IA "encuentre" que no tenga fuente en ese modelo no es un gap real — es opinión. Ver AGENTS.md sección SDLC para el mapeo completo de fases.

---

## 7. Cuándo el Agente PAUSA y me consulta

El agente debe pausar y esperar mi respuesta cuando:

```
□ Terminó un bloque del ROADMAP — no continúa sin mi validación (Gate 5)
□ Hay un cambio de scope implícito o explícito — clasificar en SCOPE-MANAGEMENT.md (Gate 7)
□ Encontró un breaking change no anticipado en el RESOLVER-GAPS.md
□ Hay un error que no puede resolver en 3 intentos
□ La implementación requiere una decisión de diseño que no está en los docs
□ Hay un DECISIÓN-GAP bloqueante sin respuesta del cliente
□ Algo que iba a hacer podría afectar datos de usuarios reales
□ El contexto de la sesión se está agotando (más de 70% de la ventana usada)
□ Va a hacer algo que afecte infraestructura (entornos, secrets, CI/CD) sin INFRA.md aprobado
□ Va a deployar a producción sin el checklist de DEPLOY.md completado
```

El agente **NO** pausa cuando:
```
□ Es un fix puntual de un error obvio
□ Es formateo o naming que ya está definido en TECHLEAD.md
□ Es un test para código que ya implementó
□ La decisión ya está documentada en algún .md del proyecto
□ Es un cambio Tipo A (corrección de entendimiento mío) — se corrige directo
```

---

## 8. Autonomía Gradual

El agente gana autonomía según demuestra confiabilidad en ejecuciones anteriores.

**Nivel 1 (proyecto nuevo / colaborador nuevo):** Pausa después de cada tarea pequeña.
**Nivel 2 (proyecto conocido):** Pausa solo después de cada bloque del ROADMAP.
**Nivel 3 (alta confianza):** Pausa solo en los Gates HITL obligatorios (1, 2, 3, 6).

El nivel lo asigno yo explícitamente al inicio de cada sesión. Si no lo digo, el agente asume Nivel 1.

Para colaboradores humanos, la autonomía gradual sigue el mismo sistema — ver COLLABORATION.md sección 3.

---

## 9. Qué hacer con los DECISIÓN-GAP (Abiertos del cliente)

**Camino A — Lo resuelvo yo:**
Es una decisión técnica o de diseño que puedo tomar. La tomo, la documento en RESOLVER-GAPS.md, y el gap se cierra.

**Camino B — Necesita respuesta del cliente:**
Escribo la pregunta específica (no vaga) y la mando al cliente.

**Ejemplo correcto:** "¿Cuándo decís que el exercise input no debería ser un modal, querés que sea una página separada, un panel inline, o algo diferente?"

**Ejemplo incorrecto:** "¿Cómo querés el exercise input?"

**Regla:** Si hay más de 3 DECISIÓN-GAP sin respuesta del cliente, el proyecto se pausa. No se implementa sobre ambigüedad.

---

## 10. HITL en Operación (post-lanzamiento)

El HITL no termina con el deploy. En la fase de operación:

**Triage de incidentes:** Yo clasifico severidad P0/P1/P2/P3 — el agente no escala sin mi validación. Ver OPERATIONS.md sección 2.

**Comunicación al cliente:** Siempre pasa por mí. El agente prepara el draft, yo lo reviso y envío.

**Mejoras post-launch:** Vuelven al inicio del SDLC — CLIENT-REQUIREMENTS nueva versión → GAPS → RESOLVER → ROADMAP. El agente no implementa mejoras sin el ciclo completo.

**Cambios de scope en producción:** Siguen el mismo protocolo de SCOPE-MANAGEMENT.md — clasificar, evaluar impacto, documentar decisión.

---

*Versión: 3.0 | 2026-04-07*
*Este archivo es la constitución personal de trabajo con IA. El agente lo lee para entender cuándo ejecutar y cuándo pausar.*
*Referencias: AGENTS.md, EXTRACTION-PROMPT.md, GAPS-PROMPT.md, RESOLVER-PROMPT.md, INFRA.md, DEPLOY.md, OPERATIONS.md, COLLABORATION.md, SCOPE-MANAGEMENT.md*

## 11. Roblox workflow y herramientas de "co-thinking"

- **Herramientas:** Uso tanto el Roblox Studio oficial (interactivo) como `robloxstudio-mcp` (https://github.com/boshyxd/robloxstudio-mcp) para flujos automatizados, integración con MCP y pruebas en CI-like workflows.
- **Roles de las herramientas:** Preferir Roblox Studio para diseño interactivo y depuración manual; usar `robloxstudio-mcp` para runs automatizados, pipelines y tareas repetibles.
- **Modelos/co-thinking:** `Copilot` es la herramienta principal para generación e implementación de código dentro del repositorio. `Claude Web` se usa como "co-thinker" cuando se necesita brainstorming, generación de boilerplate, o trabajo de diseño de alto nivel en game dev/Roblox. No reemplaza a Copilot en implementaciones, pero es la herramienta preferida para ideas, arquitectura de gameplay y tareas conceptuales.
- **Persistencia:** Cuando una decisión de diseño o una resolución importante se toma con `Claude Web`, el agente debe registrar un resumen en Engram (topic_key sugerido: `tools/claude-web`) y en el artefacto correspondiente (`RESOLVER-GAPS.md`, `ROADMAP.md`, o el doc relevante) para trazabilidad.
