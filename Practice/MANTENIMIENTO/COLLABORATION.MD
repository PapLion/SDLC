# COLLABORATION.md — Gestión Transversal y Trabajo con Otros Humanos

> Este documento cubre el protocolo para cuando no trabajas solo.
> Aplica cuando hay un colaborador humano, un cliente técnico, o alguien que necesita
> ser onboardeado a un proyecto tuyo.
>
> **Principio fundamental:** Tu metodología SDLC es el contrato de trabajo.
> Cualquier persona que entre al proyecto, entra a la metodología — no al revés.
>
> **Cuándo usar este documento:**
> → Un cliente te manda a un colaborador o desarrollador externo
> → Necesitás contratar o trabajar con otro dev en un proyecto
> → Alguien necesita tomar el proyecto si no estás disponible

---

## 1. Posición en el SDLC

Este documento es **transversal** — aplica en cualquier fase del SDLC cuando hay más de un humano involucrado.

```
ANÁLISIS → DISEÑO → IMPLEMENTACIÓN → QA → DEPLOY → OPERACIÓN
    ↕          ↕           ↕           ↕      ↕         ↕
              [COLLABORATION.md — aplica en cualquier punto]
```

---

## 2. Onboarding de un Colaborador Humano

### Documentos que debe leer (en este orden)
```
1. AGENTS.md          → La metodología completa, stack, estándares
2. HITL.md            → Filosofía de trabajo y validación
3. COLLABORATION.md   → Este archivo — cómo trabajamos juntos
4. ARCHITECH.md       → Decisiones arquitectónicas del proyecto específico
5. TECHLEAD.md        → Stack técnico y estándares del proyecto
6. ROADMAP.md         → Qué se está construyendo y en qué orden
```

### Checklist de onboarding
```
□ Leyó AGENTS.md completo y no tiene preguntas bloqueantes
□ Tiene acceso al repositorio (con permisos correctos — nunca admin a nuevos)
□ Puede hacer clone, instalar dependencias, y correr el proyecto en local
□ Entendió la arquitectura del proyecto (ARCHITECH.md)
□ Entendió el flujo de PR — nunca push directo a main
□ Tiene acceso a las variables de entorno de desarrollo (nunca las de producción)
□ Puede correr los tests y están en verde
□ Revisó el ROADMAP.md — sabe en qué bloque estamos y cuál es el siguiente
□ Sabe cómo crear una rama, hacer un commit convencional, y abrir un PR
```

### Lo que un colaborador NO recibe nunca
```
❌ Variables de entorno de producción
❌ Acceso directo a la DB de producción
❌ Permisos para mergear a main sin review
❌ Secrets de servicios críticos (Stripe, servicios de pago, etc.)
❌ Acceso al panel de administración del hosting en producción
```

---

## 3. Roles y División de Responsabilidades

### Cuando hay dos desarrolladores en el proyecto

| Responsabilidad | Owner | Colaborador |
|---|---|---|
| Arquitectura y decisiones técnicas | Dani | Consulta y aporta input |
| Code review de PRs | Dani (aprobación final) | Puede reviewar también |
| Merge a main | Solo Dani | Nunca |
| Deploy a producción | Solo Dani | Nunca (hasta autonomía gradual) |
| Implementación de bloques del ROADMAP | Ambos | Según asignación |
| Comunicación con el cliente | Dani | Solo si Dani lo delega explícitamente |
| Actualización de AGENTS.md / ARCHITECH.md | Dani | Propone cambios, no edita directo |

### Autonomía gradual para colaboradores
Similar al HITL.md — el colaborador gana autonomía según demuestra criterio.

```
Nivel 1 (primeras 2 semanas):
→ Trabaja solo en tareas de ROADMAP asignadas
→ Todo PR requiere review de Dani antes de merge
→ No toma decisiones de arquitectura

Nivel 2 (después de 3+ PRs sin issues mayores):
→ Puede mergear PRs pequeños (fixes, docs) sin review si CI está verde
→ Puede proponer cambios a la arquitectura (con justificación)
→ Puede estimar tareas del ROADMAP de forma autónoma

Nivel 3 (trust establecido):
→ Puede gestionar bloques completos del ROADMAP de forma autónoma
→ Presenta el bloque finalizado con sus criterios de Done completos
→ Dani hace spot-check en lugar de review completo
```

---

## 4. Flujo de Trabajo Compartido

### Asignación de tareas
```
Fuente de verdad: ROADMAP.md (bloques y sus criterios de Done)

Asignación explícita:
→ Cada bloque tiene un owner asignado
→ Si no está asignado, nadie lo toca hasta que Dani lo asigne
→ No se puede empezar el siguiente bloque hasta que el anterior pasó su gate HITL
```

### Comunicación de estado
```
Al empezar un bloque:
→ "Estoy en BLOQUE X — [nombre]"

Al terminar un bloque:
→ "BLOQUE X listo — PR abierto [link]"
→ "Criterios de Done: [lista de qué se verificó]"

Si hay un bloqueante:
→ "Bloqueado en BLOQUE X — [descripción del problema]"
→ No esperar — avisar apenas aparece el bloqueante
```

### Resolución de conflictos técnicos
```
Si hay desacuerdo en una decisión técnica:
1. El colaborador presenta su propuesta con justificación (por qué es mejor)
2. Dani evalúa y decide
3. La decisión se documenta en ARCHITECH.md o TECHLEAD.md con fecha
4. Una vez documentada, no se discute más — se implementa

Regla: los desacuerdos se resuelven con argumentos técnicos y documentación,
nunca con "así lo prefiero yo" de ninguna de las partes.
```

---

## 5. Gestión de Pull Requests con Varios Autores

### Reglas adicionales cuando hay colaboradores
```
□ El PR describe EXACTAMENTE qué bloque del ROADMAP resuelve
□ El PR incluye los criterios de Done del bloque y confirma que todos pasan
□ El colaborador no mergeea su propio PR — Dani lo aprueba
□ Si el CI falla, el colaborador lo corrige antes de pedir review
□ PRs abiertos más de 3 días sin actividad se cierran y se reabre cuando está listo
```

### Code review con colaboradores — qué busco yo (Dani)
```
□ Sigue la arquitectura definida en ARCHITECH.md
□ Sigue las convenciones de TECHLEAD.md
□ Tests incluidos para lógica crítica
□ No rompe ningún test existente
□ No introduce deuda técnica innecesaria
□ El código que no entiendo tiene comentario explicativo

Si hay que rechazar el PR:
→ Siempre con razón específica y acción concreta requerida
→ "Cambiar X porque Y" — nunca "esto no está bien"
```

> Referencia: Code_Review_BestPractices.md, Code_Review_Pyramid.md (Theory/)

---

## 6. Transferencia de Proyecto (Handoff)

Para cuando alguien necesita tomar el proyecto — ya sea temporalmente o de forma permanente.

### Documentación mínima para un handoff exitoso
```
□ AGENTS.md actualizado y refleja el estado real del proyecto
□ ARCHITECH.md con todas las decisiones tomadas y su justificación
□ ROADMAP.md con estado actual de cada bloque (Done / En curso / Pendiente)
□ TECH-DEBT.md actualizado
□ .env.example con TODAS las variables necesarias (sin valores, solo keys)
□ README.md del proyecto con instrucciones de setup local paso a paso
□ Engram con los aprendizajes y contexto del proyecto
□ Lista de accesos necesarios (sin los secrets — esos se transfieren por canal seguro)
```

### Sesión de handoff (si hay tiempo para hacerla)
```
1. Walk-through del README — verificar que quien recibe puede instalar y correr el proyecto
2. Walk-through de la arquitectura (ARCHITECH.md) — preguntas y respuestas
3. Walk-through del ROADMAP — estado actual, próximos pasos
4. Identificar las partes del código con más deuda técnica (TECH-DEBT.md)
5. Accesos y credenciales transferidos por canal seguro
6. Contacto para dudas post-handoff (si aplica)
```

---

## 7. Trabajo con Clientes Técnicos

A veces el cliente tiene criterio técnico y quiere participar activamente.

### Cuándo es positivo
```
→ El cliente puede hacer code review y aportar contexto de negocio que ningún dev tiene
→ El cliente puede validar el ROADMAP técnicamente antes de que empiece la implementación
→ El cliente puede identificar edge cases que ningún análisis de requirements captura
```

### Cuándo hay que establecer límites
```
→ El cliente no puede modificar AGENTS.md ni ARCHITECH.md sin consenso
→ El cliente no puede hacer push directo a main
→ El cliente no puede cambiar el scope del ROADMAP durante la implementación de un bloque
   (los cambios de scope van a la siguiente versión de CLIENT-REQUIREMENTS.md)
→ El cliente da feedback sobre QUÉ se construye, no sobre CÓMO se implementa
   (el cómo es decisión técnica del dev)
```

---

## 8. Anti-patrones — Lo que nunca hacemos en colaboración

```
❌ Asignar trabajo sin documentar el bloque del ROADMAP correspondiente
❌ Mergear a main sin que CI esté verde, independientemente de quién escribió el código
❌ Dar acceso a producción a colaboradores sin evaluar nivel de autonomía primero
❌ Tomar decisiones arquitectónicas verbalmente — siempre documentadas
❌ "Lo revisamos después del merge" — el review va ANTES del merge
❌ Cambiar la arquitectura sin actualizar ARCHITECH.md
❌ Resolver desacuerdos con urgencia en lugar de con argumentos
❌ Asumir que alguien entendió algo — confirmar explícitamente
```

---

*Versión: 1.0 | 2026-04-07*
*Referencia: Code_Review_BestPractices.md, Code_Review_Pyramid.md, Technical-Writer.md (Theory/)*
*Posición SDLC: Transversal — aplica en todas las fases cuando hay más de un humano*