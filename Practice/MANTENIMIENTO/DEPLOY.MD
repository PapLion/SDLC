# DEPLOY.md — Fase de Despliegue / Release

> Este documento formaliza la fase de despliegue dentro del SDLC personalizado.
> Se ejecuta DESPUÉS de que todos los bloques del ROADMAP.md pasaron su gate HITL.
> El agente de implementación lo lee antes de cualquier acción que afecte producción.
>
> **Principio fundamental:** Producción exige confianza, no esperanza.
> Un deploy sin checklist no es un deploy — es una apuesta.

---

## 1. Posición en el SDLC

```
... → QA → [DEPLOY.md] → OPERACIÓN
              ↑
      Este documento
```

**Entrada:** Todos los bloques del ROADMAP.md con criterios de Done completos y gates HITL aprobados.
**Salida:** Sistema en producción, tag de versión creado, release notes publicadas.

---

## 2. Estrategia de Versionado — Release Please

El versionado automático elimina la burocracia y asegura documentación consistente.

### Flujo automático
```
merge en main
    ↓
Release Please crea un Release PR automáticamente
    ↓
[HITL: revisar el Release PR — versión correcta, changelog completo]
    ↓
merge del Release PR
    ↓
Deploy a producción
Tag vX.Y.Z creado en git
GitHub Release publicado
Release notes generadas (con IA si el proyecto lo tiene configurado)
```

### Convención de versiones (Semantic Versioning)
```
MAJOR.MINOR.PATCH

MAJOR → cambios que rompen compatibilidad (breaking changes)
MINOR → features nuevas sin romper lo existente
PATCH → fixes, mejoras menores, correcciones

Ejemplos:
  v1.0.0 → primera release estable
  v1.1.0 → nueva feature
  v1.1.1 → fix de bug en producción
  v2.0.0 → refactor mayor / cambio de API
```

> Referencia: Devops.md (Theory/) — sección CI/CD, Release Management

---

## 3. Variables de Entorno en Producción

### Regla de oro
La app debe fallar en el arranque si falta una variable crítica. Nunca en runtime.

### Checklist de variables antes de deploy
```
□ Todas las variables del .env.example están seteadas en producción
□ No hay valores de desarrollo (localhost, test_db) en producción
□ JWT_SECRET tiene al menos 32 chars random (nunca el mismo que dev)
□ DATABASE_URL apunta a la DB de producción, no a la local
□ API keys de terceros son las de producción, no las de sandbox
□ NODE_ENV=production está seteado
```

### Gestión de secrets por entorno
```
Development:   .env.local          → en .gitignore, valores locales
Staging:       Variables del CI/CD → GitHub Actions Secrets
Production:    Variables del CI/CD → GitHub Actions Secrets / Vault
```

**Nunca:** secrets en el código fuente, en el historial de git, ni en comentarios.

> Referencia: SECURITY.md sección Variables de Entorno, Api-Security.md (Theory/)

---

## 4. Checklist Pre-Deploy

Completar antes de cualquier merge que vaya a producción.

### CI/CD
```
□ Build pasa sin errores (npm run build / compilación limpia)
□ Lint pasa sin warnings (npm run lint / ruff)
□ Tests en verde (npm run test / pytest)
□ Security review completado (claude-code-security-review o manual)
□ No hay secrets hardcodeados detectados
```

### Código
```
□ Todos los bloques del ROADMAP.md están Done
□ No hay console.log / print de debugging en código de producción
□ No hay TODOs críticos sin resolver que bloqueen funcionalidad del cliente
□ Las migraciones de DB están incluidas y probadas en local limpio
□ Los índices de DB necesarios están en las migraciones
```

### Funcional
```
□ El flujo principal del cliente funciona end-to-end en staging/local
□ Los casos críticos de QA.md pasaron su validación manual
□ Si hay breaking changes: plan de migración documentado
□ Si hay cambios de schema: migración probada con rollback conceptual
```

---

## 5. Estrategia de Rollback

**Regla:** Antes de cada deploy, saber exactamente cómo deshacerlo.

### Rollback de código
```bash
# Opción 1 — Revert del merge commit (preferida para hotfixes)
git revert <merge-commit-hash>
git push origin main

# Opción 2 — Deploy del tag anterior
# Volver a deployar la imagen/build del tag anterior
# Depende de la plataforma (Vercel, Railway, etc.)
```

### Rollback de base de datos
```
ANTES de cada deploy con migración:
→ Tomar backup manual de la DB de producción
→ Documentar el rollback SQL en la misma migración

Si el deploy falla con migración aplicada:
→ Restaurar desde backup
→ Deployar la versión anterior del código
→ Nunca intentar "deshacer" una migración que ya tocó datos reales sin backup
```

### Criterios para ejecutar rollback
```
🔴 Rollback inmediato si:
   → El sistema no arranca en producción
   → Hay pérdida o corrupción de datos
   → Falla de autenticación / usuarios no pueden acceder
   → Error 500 en más del 5% de las requests

🟡 Evaluar en los próximos 30 min si:
   → Performance degradada significativamente
   → Feature crítica del cliente no funciona
   → Logs con errores inesperados elevados
```

---

## 6. Health Checks Post-Deploy

Ejecutar dentro de los primeros 10 minutos después de cada deploy.

### Verificaciones inmediatas
```
□ La app arrancó sin errores en los logs de startup
□ El endpoint de health check responde (/ o /health o /api/health)
□ La conexión a DB está activa
□ El flujo de login / autenticación funciona
□ El flujo principal del cliente funciona
□ Los logs no muestran errores críticos inesperados
```

### Verificación de variables de entorno
```
□ La app leyó todas las variables correctamente (verificar en logs de startup)
□ Las conexiones a servicios externos están activas (APIs de terceros, etc.)
```

### Monitoreo post-deploy (primeras 24hs)
```
→ Revisar logs cada 2-4 horas el primer día
→ Prestar atención a: error rates, response times, DB queries
→ Tener el plan de rollback listo durante las primeras 24hs
```

> Referencia: System-Design.md sección Monitoring, Devops.md (Theory/)

---

## 7. Tipos de Deploy por Urgencia

### Deploy estándar (nueva feature / bloque del roadmap)
```
Proceso: PR → CI verde → merge → Release Please PR → HITL → merge → auto-deploy
Tiempo estimado: 1-3 días (incluye review y checklist completo)
```

### Hotfix (bug crítico en producción)
```
Proceso: rama hotfix → PR urgente → CI verde → HITL express → merge → tag de patch
Tiempo estimado: horas, no días
Shortcuts aceptados: checklist mínimo, sin Release Please (tag manual)
Shortcuts NO aceptados: saltarse CI, saltarse backup de DB, saltarse health check
```

### Deploy de migración de datos
```
Proceso: SIEMPRE fuera del horario de mayor uso
Pasos adicionales: backup antes, ventana de mantenimiento si aplica, rollback listo
HITL: aprobación explícita antes de ejecutar la migración en producción
```

---

## 8. CI/CD con GitHub Actions — Referencia

### Workflow básico por PR
```yaml
# .github/workflows/ci.yml
name: CI
on: [pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: npm ci            # o pip install / equivalente
      - name: Lint
        run: npm run lint
      - name: Tests
        run: npm run test
      - name: Build
        run: npm run build
```

### Workflow de deploy (en merge a main)
```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production    # requiere aprobación manual si está configurado
    steps:
      - uses: actions/checkout@v4
      - name: Deploy
        run: [comando de deploy de la plataforma]
        env:
          [variables de entorno desde GitHub Secrets]
```

> Referencia: Devops.md (Theory/) — GitHub Actions, CI/CD pipelines

---

## 9. Checklist Post-Deploy — Firma de Release

Completar después de cada deploy exitoso.

```
□ Health checks post-deploy completados
□ Tag de versión creado en git (Release Please o manual)
□ GitHub Release publicado con changelog
□ Engram actualizado con aprendizajes del deploy (si los hay)
□ Fecha de deploy registrada

Versión desplegada: [vX.Y.Z]
Fecha: [YYYY-MM-DD]
Aprobado por: Dani (Panda)
Rollback disponible: sí → versión [vX.Y.Z-anterior]
```

---

## 10. Anti-patrones — Lo que nunca hacemos

```
❌ Push directo a main (siempre vía PR)
❌ Deploy sin que pasen CI + tests
❌ Deploy con migraciones sin backup previo
❌ "Lo arreglamos después del deploy" → si no está listo, no se deploya
❌ Variables de producción hardcodeadas en el código
❌ Deploy en viernes tarde o antes de vacaciones (Murphy's Law)
❌ Rollback sin haber probado que funciona antes de necesitarlo
```

---

*Versión: 1.0 | 2026-04-07*
*Referencia: Devops.md, System-Design.md, Api-Security.md (Theory/)*
*Posición SDLC: Fase 5 — Despliegue / Release*