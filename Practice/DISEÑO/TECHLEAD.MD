# TECHLEAD.md — Decisiones Técnicas del Proyecto

> Este documento es la guía de implementación para el agente de código.
> Cada decisión técnica está tomada, justificada y es final.
> El agente no decide — implementa según lo que está aquí.
> Completar este archivo ANTES de empezar a implementar.

---

## 1. Stack del Proyecto

```
Runtime:      [Node.js 20 LTS / Python 3.12 / etc.]
Lenguaje:     [TypeScript strict / Python + type hints]
Framework:    [Next.js 15 / FastAPI / Discord.js / etc.]
Base de datos:[PostgreSQL / SQLite / MongoDB]
ORM/Driver:   [Drizzle / Prisma / SQLAlchemy]
Testing:      [Vitest / pytest / Jest]
Linter:       [ESLint + @typescript-eslint / Ruff]
Formatter:    [Prettier / Ruff]
CI/CD:        [GitHub Actions]
```

> Completar con versiones exactas fijadas. Referencia: Backend.md, Frontend.md (Theory/)

### Scripts obligatorios del proyecto
```json
{
  "dev":    "<comando de desarrollo con hot reload>",
  "build":  "<comando de compilación>",
  "start":  "<comando de arranque en producción>",
  "lint":   "<comando de linting>",
  "format": "<comando de formateo>",
  "test":   "<comando de tests>",
  "test:watch": "<tests en modo watch>"
}
```

---

## 2. Decisiones Técnicas — Registro

Completar con las decisiones del proyecto. Formato:

| Decisión | Elegido | Descartado | Razón |
|---|---|---|---|
| [ej: Base de datos] | [PostgreSQL] | [MySQL, SQLite] | [escala, features, familiaridad] |
| [ej: ORM] | [Drizzle] | [Prisma, TypeORM] | [control, rendimiento, DX] |
| [ej: Estado] | [Zustand] | [Redux, Jotai] | [simplicidad, tamaño] |
| [ej: Auth] | [JWT + refresh] | [sessions, API keys] | [stateless, escala horizontal] |

> Referencia: Software-Design-and-Architecture.md, System-Design.md (Theory/)

---

## 3. Arquitectura del Proyecto

Definir la arquitectura antes de implementar. Opciones comunes:

```
LAYERED (recomendada para proyectos medianos):
├── presentation/   → UI, API routes, handlers — todo lo que toca el exterior
├── application/    → orquestación, casos de uso, coordinación entre capas
├── domain/         → lógica de negocio pura — sin frameworks, sin DB
└── infrastructure/ → DB, cache, servicios externos, logging

FEATURE-BASED (recomendada para proyectos grandes):
├── features/
│   ├── auth/       → todo lo de autenticación junto
│   ├── users/      → todo lo de usuarios junto
│   └── payments/   → todo lo de pagos junto
└── shared/         → utilidades, tipos, configuración compartida

MONOLITH SIMPLE (recomendada para MVPs):
├── routes/         → endpoints
├── services/       → lógica de negocio
├── models/         → modelos de DB
└── utils/          → utilidades
```

**Arquitectura elegida para este proyecto:** [definir aquí]

**Regla fundamental:** [definir la regla de dependencias — ej: "las dependencias solo apuntan hacia abajo"]

> Referencia: Software-Design-and-Architecture.md (Theory/)

---

## 4. Estructura de Carpetas

```
[nombre-proyecto]/
├── src/
│   └── [completar con la estructura real]
├── tests/
├── .env.example
├── .gitignore
└── package.json / pyproject.toml
```

---

## 5. Estándares de Código

### Convenciones de naming

```
[TypeScript]
Clases/Interfaces:  PascalCase   → UserService, GameSession
Funciones/vars:     camelCase    → getUserById, sessionId
Constantes:         UPPER_SNAKE  → MAX_RETRIES, DEFAULT_TIMEOUT
Archivos:           PascalCase   → UserService.ts (clases)
                    kebab-case   → use-auth.ts (hooks/utils)
Enums:              PascalCase   → UserRole.ADMIN

[Python]
Clases:             PascalCase   → UserService
Funciones/vars:     snake_case   → get_user_by_id
Constantes:         UPPER_SNAKE  → MAX_RETRIES
Archivos:           snake_case   → user_service.py
```

### Reglas que el agente NUNCA viola

```
❌ NUNCA usar `any` en TypeScript — usar `unknown` y narrowing
❌ NUNCA queries SQL con strings concatenados — solo prepared statements / ORM
❌ NUNCA secretos hardcodeados — siempre process.env / os.environ
❌ NUNCA `console.log` en producción — usar logging estructurado
❌ NUNCA funciones de más de ~50 líneas sin justificación
❌ NUNCA mezclar capas — Presentation no toca DB, Domain no toca framework
❌ NUNCA magic numbers — usar constantes con nombre

✅ SIEMPRE try/catch en métodos async críticos
✅ SIEMPRE tipar retornos de funciones explícitamente
✅ SIEMPRE un archivo = una responsabilidad
✅ SIEMPRE errores tipados, nunca throw new Error('string genérico')
```

> Referencia: Code_Review_BestPractices.md, Api-Security.md (Theory/)

---

## 6. Patrones de Diseño Aplicados

Documentar los patrones elegidos para este proyecto. No usar todos — solo los que resuelven problemas reales.

### Patrones probables según tipo de proyecto

```
Web API / Backend:
→ Repository       — abstrae el acceso a DB
→ Service Layer    — lógica de negocio entre routes y repositories  
→ Factory          — crea instancias complejas
→ Middleware Chain — validación, auth, rate limiting en secuencia
→ Adapter          — normaliza servicios externos heterogéneos

Frontend / Full-stack:
→ Compound Component — componentes que comparten estado implícito
→ Custom Hook       — lógica reutilizable desacoplada de la UI
→ Observer          — reactividad entre componentes desacoplados

Sistemas complejos:
→ Facade            — simplifica subsistemas complejos
→ Strategy          — algoritmos intercambiables
→ State             — comportamiento según estado finito
→ Event-Driven      — comunicación desacoplada entre módulos
```

**Patrones elegidos para este proyecto:**
| Patrón | Dónde | Por qué |
|---|---|---|
| [patrón] | [ubicación] | [razón] |

> Referencia: Software-Design-and-Architecture.md (Theory/)

---

## 7. Errores Tipados

```typescript
// Jerarquía base — adaptar según el dominio del proyecto
export class AppError extends Error {
  constructor(message: string, public readonly code: string) {
    super(message)
    this.name = 'AppError'
  }
}

// Errores de dominio — uno por área de negocio
export class AuthError extends AppError {}
export class ValidationError extends AppError {}
export class NotFoundError extends AppError {}
export class ConflictError extends AppError {}

// Códigos de error estandarizados
export const ERROR_CODES = {
  // Auth
  UNAUTHORIZED:       'AUTH_001',
  FORBIDDEN:          'AUTH_002',
  TOKEN_EXPIRED:      'AUTH_003',
  // Validación
  INVALID_INPUT:      'VAL_001',
  MISSING_FIELD:      'VAL_002',
  // Negocio (completar según el proyecto)
  // ...
}
```

> Regla: `throw new AuthError('msg', ERROR_CODES.UNAUTHORIZED)` — nunca `throw new Error('string')`

---

## 8. Git — Estructura de Ramas

```
main / master        ← producción, siempre deployable
  └── develop        ← integración (si el equipo > 1)
        ├── feat/<descripción>
        ├── fix/<descripción>
        └── refactor/<descripción>
```

Adaptar según si es proyecto solo o en equipo. Referencia: PR.md para convención de commits.

---

## 9. Configuración y Variables de Entorno

```bash
# .env.example — siempre en git, sin valores reales
# Variables críticas del proyecto

# App
NODE_ENV=development
PORT=3000

# Database
DATABASE_URL=          # postgresql://user:pass@host:5432/db

# Auth
JWT_SECRET=            # min 32 chars, random
JWT_EXPIRES_IN=15m

# [Servicios externos específicos del proyecto]
```

**Regla:** La app falla en startup si falta una variable crítica. Nunca en runtime.

```typescript
// Validación al arrancar
import { z } from 'zod'

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  JWT_SECRET:   z.string().min(32),
  NODE_ENV:     z.enum(['development', 'production', 'test']),
})

export const env = envSchema.parse(process.env) // falla aquí o no falla nunca
```

> Referencia: SECURITY.md, Api-Security.md (Theory/)

---

## 10. Criterios de Done por Bloque

Adaptar con los bloques reales del ROADMAP.md del proyecto.

Estructura base:
```
BLOQUE X — [Nombre]
✅ [funcionalidad principal trabajando end-to-end]
✅ [tests críticos en verde]
✅ [validación manual completada]
✅ [PR mergeado y build en verde]
```

**Un bloque está DONE solo cuando cumple todo. No hay bloque "casi done".**

---

## 11. Performance — Decisiones Base

```
Caching:    [definir qué se cachea, TTL, estrategia — Cache-Aside / Write-Through]
Pagination: [cursor-based o offset, tamaño de página por defecto]
Rate limit: [límites por endpoint, ventana de tiempo]
Índices DB: [qué columnas necesitan índice — ver DATABASE.md]
```

> Referencia: Backend-performance.md, Api-Design.md (Theory/)

---

## 12. Monitoreo y Logging

```
Logger:     [Winston / pino / logging stdlib de Python]
Niveles:    ERROR → fallos críticos, excepciones no manejadas
            WARN  → situaciones recuperables, fallbacks activados
            INFO  → lifecycle del sistema, acciones de negocio
            DEBUG → solo en development

Formato:    JSON estructurado en producción
            Human-readable en desarrollo
```

**Regla:** Nunca loggear contraseñas, tokens, o PII. Filtrar antes de escribir.

> Referencia: Backend.md, System-Design.md — sección Monitoring (Theory/)

---

## 13. Checklist Tech Lead — Antes de Implementar

```
□ Stack fijado con versiones exactas
□ Arquitectura elegida y justificada
□ Estructura de carpetas definida
□ Patrones elegidos documentados
□ Errores tipados definidos
□ Variables de entorno documentadas en .env.example
□ Criterios de Done por bloque definidos
□ Reglas de código que el agente no puede violar
□ Git branches definidas
```

---

*Referencia general: Software-Design-and-Architecture.md, System-Design.md, Backend.md, Api-Design.md (Theory/)*