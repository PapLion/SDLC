# OPERATIONS.md — Fase de Operación y Mantenimiento

> Este documento cubre el ciclo post-lanzamiento de cualquier proyecto.
> Se activa cuando el sistema está vivo en producción y hay usuarios reales.
> El agente lo consulta cuando hay bugs reportados, incidentes, o mejoras post-launch.
>
> **Principio fundamental:** Un sistema en producción nunca está "terminado".
> La operación es una fase permanente, no un estado final.

---

## 1. Posición en el SDLC

```
... → DEPLOY → [OPERATIONS.md] → (ciclo de mejora continua)
                    ↑
            Este documento
            Se usa indefinidamente
            mientras el sistema está vivo
```

**Cuándo empieza:** Desde el momento del primer deploy a producción.
**Cuándo termina:** Cuando el proyecto se discontinúa formalmente.

---

## 2. Triage de Bugs Reportados

### Clasificación de severidad

```
🔴 P0 — Crítico (respuesta en horas)
   → El sistema está caído o inaccesible
   → Hay pérdida o corrupción de datos
   → Fallo de autenticación / usuarios no pueden operar
   → Brecha de seguridad activa
   Acción: hotfix inmediato, notificar al cliente, rollback si aplica

🟠 P1 — Alto (respuesta en 24-48hs)
   → Feature crítica del cliente no funciona
   → Workaround existe pero es muy incómodo
   → Afecta a la mayoría de los usuarios
   Acción: planificar fix en el próximo ciclo, comunicar ETA al cliente

🟡 P2 — Medio (respuesta en próximo sprint/ciclo)
   → Feature secundaria con comportamiento incorrecto
   → Afecta a pocos usuarios o casos edge
   → Workaround razonable disponible
   Acción: añadir al backlog de mejoras con prioridad

⚪ P3 — Bajo / Mejora (sin urgencia)
   → Comportamiento confuso pero no roto
   → Mejora de UX no crítica
   → Deuda técnica interna
   Acción: documentar en TECH-DEBT.md o backlog
```

### Proceso de triage
```
Bug reportado (cliente, logs, monitoreo)
    ↓
[HITL: Dani clasifica severidad P0/P1/P2/P3]
    ↓
P0/P1: crear issue con reproducción + contexto → hotfix o fix urgente
P2/P3: añadir a backlog → priorizar en próximo ciclo
    ↓
Comunicar al cliente (si corresponde): qué pasó, cuándo se resuelve
```

### Información mínima de un bug bien documentado
```
Título:         [descripción corta del comportamiento incorrecto]
Severidad:      [P0/P1/P2/P3]
Pasos para reproducir: [secuencia exacta que lo triggerá]
Comportamiento actual:  [qué pasa]
Comportamiento esperado: [qué debería pasar]
Evidencia:      [logs, screenshot, error message]
Contexto:       [usuario afectado, entorno, versión desplegada]
```

> Referencia: Code_Review_BestPractices.md (Theory/) — sección Bug Reports

---

## 3. Escalación de Incidentes

### Niveles de escalación
```
Nivel 1 — Tú mismo (mayoría de los casos)
   → Revisas logs, identificas causa, aplicas fix
   → Tiempo esperado: horas para P1, días para P2

Nivel 2 — Cliente informado (P0 y P1)
   → Comunicación proactiva antes de que el cliente reporte
   → Formato: qué pasó, impacto, qué estás haciendo, ETA
   → No esperes tener el fix para avisar — avisa cuando lo detectas

Nivel 3 — Terceros (si el problema está en una dependencia externa)
   → Proveedor de hosting, servicio de pagos, API de terceros
   → Documentar el issue, abrir ticket, establecer workaround temporal
```

### Template de comunicación de incidente al cliente
```
ASUNTO: [Nombre del sistema] — Incidente detectado [fecha]

Hemos detectado [descripción breve del problema].

Estado actual: [investigando / fix en curso / resuelto]
Impacto: [qué funcionalidad está afectada]
Usuarios afectados: [estimado]
Causa raíz: [si ya se identificó, o "en investigación"]

Próximos pasos:
→ [acción 1 — ETA]
→ [acción 2 — ETA]

Actualizaré en [tiempo estimado o cuando haya novedades].
```

---

## 4. Monitoreo — Los "Ojos" del Sistema

Sin monitoreo, operar un sistema en producción es volar a ciegas.

### Qué monitorear (mínimo viable)
```
APLICACIÓN
→ Logs de errores (cualquier ERROR o WARN no esperado)
→ Uptime / disponibilidad del servicio
→ Tiempo de respuesta de endpoints críticos

BASE DE DATOS
→ Conexiones activas vs. límite del pool
→ Queries lentas (> 1s en la mayoría de los casos)
→ Espacio en disco

NEGOCIO (opcional pero valioso)
→ Métricas clave del cliente: usuarios activos, transacciones, uso de features
→ Alertas cuando algo cae por debajo de un threshold esperado
```

### Herramientas según el stack
```
Logs:         Pino / Winston (Node.js) | logging stdlib (Python)
              → Siempre JSON estructurado en producción
              → Nunca loggear passwords, tokens, ni PII

Monitoreo:    Vercel Analytics / Railway Metrics (si está en esas plataformas)
              Sentry (errores en frontend y backend)
              Uptime Robot / Better Uptime (disponibilidad básica, gratis)

DB:           Supabase Dashboard / Railway DB metrics
              → Ver queries lentas, conexiones activas
```

### Revisar logs — cuándo y cómo
```
Rutina post-deploy:    Primeras 24hs — revisar cada 2-4 horas
Rutina semanal:        1 vez por semana si el sistema está estable
Rutina reactiva:       Inmediatamente cuando el cliente reporta algo
                       Inmediatamente cuando una alerta se dispara

Qué buscar en logs:
→ Cualquier ERROR o EXCEPTION no esperado
→ Warnings repetidos (pueden ser síntoma de P2 que se vuelve P1)
→ Queries lentas o timeouts a servicios externos
→ Picos de uso inusuales (puede indicar abuso o bug que genera loops)
```

> Referencia: System-Design.md sección Monitoring, Backend.md (Theory/)

---

## 5. Ciclo de Mejoras Post-Launch

Las mejoras continuas siguen el mismo SDLC que las features originales, solo que con contexto más rico porque el sistema ya está en producción.

### Tipos de input para mejoras
```
CLIENTE → Nueva funcionalidad pedida después del lanzamiento
         → Comportamiento que no le gusta aunque "funcione"
         → Pedido de cambio en reglas de negocio

TÉCNICO → Deuda técnica de TECH-DEBT.md que creció hasta molestar
         → Performance degradada detectada en monitoreo
         → Dependencias con vulnerabilidades que necesitan update

OPERACIONAL → Bug P2/P3 que se vuelve recurrente
             → Edge case descubierto en producción que no estaba en los reqs
```

### Cómo priorizar mejoras
```
Prioridad 1: Seguridad y estabilidad (siempre primero)
Prioridad 2: Lo que el cliente reportó como bloqueante
Prioridad 3: Deuda técnica que amenaza la escalabilidad del sistema
Prioridad 4: Mejoras de UX solicitadas
Prioridad 5: Nice-to-haves y optimizaciones
```

### Flujo de una mejora post-launch
```
Input identificado (bug, pedido, deuda)
    ↓
[HITL: Dani decide si entra en el ciclo ahora o va al backlog]
    ↓
Si entra: vuelve a la Fase 1 del SDLC
          CLIENT-REQUIREMENTS.md (nuevo ítem o versión nueva)
          → GAPS → RESOLVER → ROADMAP → IMPLEMENTAR → QA → DEPLOY
    ↓
Si no entra ahora: documentar en backlog con fecha y contexto
```

---

## 6. Mantenimiento Preventivo

### Dependencias y seguridad
```
Frecuencia: mensual (o cuando hay reporte de vulnerabilidad crítica)

□ Revisar dependencias con vulnerabilidades conocidas
  → npm audit / pip-audit / dependabot alerts en GitHub
□ Actualizar dependencias con parches de seguridad
□ Evitar actualizaciones de major version sin testing

Regla: las dependencias desactualizadas son deuda de seguridad.
No necesitan actualizarse todas juntas — solo las con vulnerabilidades activas.
```

### Base de datos
```
Frecuencia: mensual

□ Verificar que los backups automáticos están funcionando
□ Revisar queries lentas y evaluar si necesitan nuevos índices
□ Limpiar datos obsoletos si aplica (logs viejos, sessions expiradas)
□ Verificar espacio en disco y proyección de crecimiento
```

### Rotación de secrets
```
Frecuencia: según política del cliente o ante cualquier sospecha de compromiso

□ Rotar JWT_SECRET si hay sospecha de exposición
□ Rotar API keys de terceros si hay uso anómalo
□ Actualizar contraseñas de DB si cambia el equipo con acceso
```

> Referencia: SECURITY.md (Theory/) — sección rotación de credentials, OWASP

---

## 7. Documentación de Aprendizajes (Engram)

Cada incidente resuelto y cada mejora implementada tiene aprendizajes.
Si no se documentan, se repiten.

### Qué guardar en Engram después de un incidente o mejora significativa
```
What:    Qué pasó / qué se cambió
Why:     Por qué ocurrió / por qué se tomó esa decisión
Where:   En qué parte del sistema / qué archivos
Learned: Qué aprendí que cambia cómo haré esto en el futuro
```

### Post-mortem de incidentes P0/P1
Para incidentes críticos, documentar adicionalmente:
```
Cronología:   Cuándo se detectó, cuándo se resolvió
Causa raíz:   Por qué ocurrió (no quién — no se buscan culpables)
Impacto real: Usuarios afectados, tiempo de inactividad, datos afectados
Fix aplicado: Qué se hizo para resolverlo
Prevención:   Qué cambio de proceso o técnico evita que vuelva a pasar
```

---

## 8. Checklist de Operación — Revisión Periódica

### Revisión semanal (cuando el sistema está activo)
```
□ Logs revisados — sin errores críticos nuevos
□ Uptime verificado
□ Backlog de issues actualizado y priorizado
□ Cualquier P0/P1 abierto tiene owner y ETA
```

### Revisión mensual
```
□ Dependencias con vulnerabilidades revisadas
□ Métricas de uso revisadas — tendencias normales o anómalas
□ Backups verificados (restaurar uno como prueba cada trimestre)
□ TECH-DEBT.md revisado — alguna deuda se volvió urgente?
□ Engram actualizado con aprendizajes del mes
```

---

*Versión: 1.0 | 2026-04-07*
*Referencia: System-Design.md, Devops.md, Backend.md, Api-Security.md (Theory/)*
*Posición SDLC: Fase 6 — Operación y Mantenimiento*