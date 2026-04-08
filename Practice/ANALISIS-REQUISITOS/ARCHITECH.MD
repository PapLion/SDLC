# ARCHITECH.md — Arquitectura de Software

> Este documento define las decisiones arquitectónicas del proyecto.
> Se completa DESPUÉS de CLIENT-REQUIREMENTS.md, GAPS-SDLC.md y RESOLVER-GAPS.md.
> El agente consulta este archivo antes de implementar cualquier módulo.

---

## 1. Paradigmas Elegidos

Seleccionar los que aplican al proyecto y justificar brevemente.

```
OOP (Object-Oriented)
→ Usar para: entidades con estado y ciclo de vida
→ Ejemplos: User, Session, Order, GameSession

Funcional
→ Usar para: transformaciones de datos puras sin estado
→ Ejemplos: validators, parsers, formatters, calculators

Event-Driven
→ Usar para: comunicación entre sistemas desacoplados
→ Ejemplos: notificaciones, integraciones, side effects

Procedural / Scripts
→ Usar para: tareas simples, scripts de migración, seeds
```

**Paradigmas de este proyecto:** [completar]

---

## 2. Estilo Arquitectónico

Elegir uno y documentar la regla de dependencias.

```
LAYERED ARCHITECTURE
┌─────────────────────────────┐
│      PRESENTATION LAYER     │  ← API routes, UI, handlers, DTOs
├─────────────────────────────┤
│      APPLICATION LAYER      │  ← Casos de uso, orquestación
├─────────────────────────────┤
│        DOMAIN LAYER         │  ← Lógica de negocio pura
├─────────────────────────────┤
│     INFRASTRUCTURE LAYER    │  ← DB, cache, servicios externos
└─────────────────────────────┘
Regla: dependencias solo hacia abajo. Domain no conoce Infrastructure.

─────────────────────────────────

FEATURE-BASED ARCHITECTURE
├── features/
│   ├── auth/          ← todo de auth junto (routes, service, model)
│   ├── users/
│   └── [dominio]/
└── shared/            ← tipos, utils, config compartidos
Regla: features son independientes entre sí. Shared no importa de features.

─────────────────────────────────

CLEAN / HEXAGONAL ARCHITECTURE
├── core/              ← domain + application (sin dependencias externas)
├── adapters/          ← implementaciones concretas (DB, API externa)
└── ports/             ← interfaces que adapters implementan
Regla: core no depende de nada externo. Todo entra/sale por puertos.
```

**Arquitectura de este proyecto:** [elegir y completar]

**Regla fundamental de este proyecto:** [escribir la regla de dependencias específica]

---

## 3. Estructura de Carpetas

```
[nombre-proyecto]/
├── src/
│   ├── [capa o feature]/
│   │   ├── [módulo]/
│   │   └── ...
│   └── index.ts / main.py
├── tests/
│   ├── unit/
│   └── integration/
├── .env.example
├── .gitignore
└── [package.json / pyproject.toml]
```

Completar con la estructura real del proyecto. Referencia: TECHLEAD.md para convenciones de naming.

---

## 4. Requisitos Formales Derivados de RESOLVER-GAPS.md

### Requisitos Funcionales Adicionales
| ID | Requisito | Gap origen |
|---|---|---|
| RF-XX | [descripción] | [G-X] |

### Requisitos No Funcionales Adicionales
| ID | Requisito | Gap origen |
|---|---|---|
| RNF-XX | [descripción] | [G-X] |

---

## 5. Patrones de Diseño Aplicados

Documentar solo los patrones que realmente se usan. No inflar.

### Creacionales
| Patrón | Dónde | Por qué |
|---|---|---|
| Factory | [ubicación] | [razón concreta] |
| Singleton | [ubicación] | [razón concreta] |

### Estructurales
| Patrón | Dónde | Por qué |
|---|---|---|
| Facade | [ubicación] | [razón concreta] |
| Adapter | [ubicación] | [razón concreta] |

### De Comportamiento
| Patrón | Dónde | Por qué |
|---|---|---|
| Strategy | [ubicación] | [razón concreta] |
| Observer | [ubicación] | [razón concreta] |
| Middleware Chain | [ubicación] | [razón concreta] |

> Referencia: Software-Design-and-Architecture.md (Theory/)

---

## 6. Flujo de Datos Global

Documentar cómo fluye una request típica de punta a punta.

```
[Origen externo — usuario, API, event]
    ↓
[Entry point — route, handler, consumer]
    ↓
[Validación / Middleware]
    ↓
[Caso de uso / Orquestador]
    ↓
    ├──→ [Servicio de dominio]
    │        └──→ [Repositorio / DB]
    │
    └──→ [Servicio externo — email, notificación, etc.]
    ↓
[Respuesta al origen]
```

Reemplazar con el flujo real del proyecto.

---

## 7. Interfaces Clave — Contratos entre Capas

Definir las interfaces que actúan como contratos entre capas antes de implementar.

```typescript
// Ejemplo — adaptar según el dominio del proyecto

// Contrato de repositorio
interface UserRepository {
  findById(id: string): Promise<User | null>
  findByEmail(email: string): Promise<User | null>
  save(user: User): Promise<User>
  delete(id: string): Promise<void>
}

// Contrato de servicio de dominio
interface AuthService {
  login(email: string, password: string): Promise<AuthResult>
  refresh(token: string): Promise<AuthResult>
  logout(userId: string): Promise<void>
}

// Contrato de notificación
interface NotificationService {
  send(userId: string, message: Notification): Promise<void>
}
```

> Principio: definir interfaces antes de implementaciones. El dominio depende de interfaces, no de clases concretas (Dependency Inversion).

---

## 8. Principios SOLID Aplicados

Documentar cómo se aplica cada principio en este proyecto específico.

```
S — Single Responsibility
    [Ejemplo concreto del proyecto]
    → "UserService solo maneja lógica de usuarios, no de autenticación"

O — Open/Closed
    [Ejemplo concreto del proyecto]
    → "Agregar nuevo proveedor de pago = nueva clase que implementa PaymentAdapter"

L — Liskov Substitution
    [Ejemplo concreto del proyecto]
    → "MySQLRepository y PostgresRepository son intercambiables en UserService"

I — Interface Segregation
    [Ejemplo concreto del proyecto]
    → "AuthService no tiene métodos de perfil de usuario"

D — Dependency Inversion
    [Ejemplo concreto del proyecto]
    → "OrderService depende de PaymentPort (interfaz), no de StripeService (impl)"
```

> Referencia: Software-Design-and-Architecture.md (Theory/)

---

## 9. Clean Code — Principios Aplicados

```
DRY (Don't Repeat Yourself)
→ [Dónde se centraliza lógica repetida en este proyecto]

KISS (Keep It Simple)
→ [Límite de líneas por función, criterio de split]

YAGNI (You Aren't Gonna Need It)
→ [Qué se decidió NO implementar y por qué]

Naming
→ [Referencia a TECHLEAD.md sección convenciones]

Error Boundaries
→ [Cómo se propagan errores entre capas en este proyecto]
```

---

## 10. Security by Design

### Threat Model del Proyecto

```
AMENAZA 1: [nombre]
→ Superficie de ataque: [dónde puede ocurrir]
→ Mitigación: [cómo se previene]

AMENAZA 2: [nombre]
→ Superficie de ataque: [dónde puede ocurrir]
→ Mitigación: [cómo se previene]

[Completar según el tipo de proyecto]
```

Amenazas comunes según contexto:
```
APIs públicas:   SQL injection, XSS, CSRF, broken auth, rate limit bypass
Apps con auth:   Privilege escalation, token theft, session fixation
File handling:   Path traversal, malicious uploads, SSRF
Pagos:           Race conditions, double-charge, amount tampering
```

### Trust Boundaries
```
NO confiamos en: [inputs del usuario, datos de terceros, etc.]
SÍ confiamos en: [variables de entorno propias, IDs generados por el sistema, etc.]
```

### Defense in Depth
```
Capa 1 — [Red / Rate limiting]
Capa 2 — [Entrada / Validación de inputs]
Capa 3 — [Autorización / Permisos]
Capa 4 — [Dominio / Reglas de negocio]
Capa 5 — [Datos / Queries preparadas]
Capa 6 — [Auditoría / Logging]
```

> Referencia: SECURITY.md, Api-Security.md (Theory/)

---

## 11. Base de Datos — Schema

Definir el schema completo antes de implementar. Referencia: DATABASE.md.

```sql
-- Tabla principal del dominio
CREATE TABLE [entidad_principal] (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  -- columnas específicas del dominio
  created_at  TIMESTAMPTZ DEFAULT NOW(),
  updated_at  TIMESTAMPTZ DEFAULT NOW()
);

-- [Repetir para cada entidad]

-- Índices
CREATE INDEX idx_[tabla]_[columna] ON [tabla]([columna]);
```

### Estrategia de Migraciones
```
migrations/
    ├── 001_initial_schema.sql
    └── 002_[descripcion].sql

Reglas:
→ Nunca modificar una migración existente
→ Siempre añadir nueva migración para cambios
→ Migrator corre automáticamente al arrancar
```

---

## 12. Asincronismo

```
Estrategia global: [async/await sobre Promises / asyncio / etc.]

Concurrencia controlada:
→ [Cómo se manejan operaciones paralelas]
→ [Cómo se previenen race conditions]
→ [Cómo se manejan timeouts]

Operaciones en background:
→ [Qué va a queues/workers y qué va inline]

Retry policies:
→ [Reintentos con backoff para operaciones críticas]
```

> Referencia: Backend-performance.md (Theory/)

---

## 13. Comunicación Interna — Eventos (si aplica)

Si el proyecto usa eventos internos:

```
[evento:nombre]  → { campo1, campo2 }
→ Quién lo emite: [módulo]
→ Quién lo escucha: [módulo(s)]
→ Qué hace al recibirlo: [acción]

Reglas:
→ Un error en un observer no debe propagarse al emisor
→ Cleanup de listeners al terminar sesiones
→ Eventos sin observers loggean warning
```

---

## 14. Performance vs Scalability — Trade-offs Aceptados

| Decisión | Performance | Scalability | Trade-off |
|---|---|---|---|
| [ej: SQLite en lugar de Postgres] | ✅ Sin setup | ⚠️ Writes secuenciales | Aceptable para < X usuarios |
| [ej: Cache en memoria] | ✅ 0ms en HIT | ⚠️ Se pierde en restart | Respaldo en DB |
| [ej: Monolito en lugar de microservicios] | ✅ Sin latencia de red | ⚠️ Escala todo junto | Aceptable para escala actual |

> Referencia: System-Design.md, Backend-performance.md (Theory/)

---

## 15. Monitoreo

```
Loggers definidos: [ver TECHLEAD.md sección Logging]

Métricas de negocio a trackear:
→ [métrica 1 — ej: requests por minuto]
→ [métrica 2 — ej: tasa de error en pagos]
→ [métrica 3]

Health checks:
→ Al arrancar: [qué se verifica]
→ Periódicos: [qué se verifica y cada cuánto]

Alertas:
→ [condición] → [canal — Slack, email, log]
```

> Referencia: System-Design.md — sección Monitoring (Theory/)

---

## 16. Checklist ARCHITECH — Antes de Implementar

```
□ Paradigmas elegidos y justificados
□ Estilo arquitectónico definido con regla de dependencias
□ Estructura de carpetas completa
□ Requisitos formales derivados de RESOLVER-GAPS.md
□ Patrones de diseño elegidos y documentados
□ Flujo de datos global documentado
□ Interfaces clave definidas como contratos
□ SOLID aplicado con ejemplos concretos del proyecto
□ Threat model completo
□ Schema de DB completo con índices
□ Trade-offs de performance/scalability aceptados
□ Plan de monitoreo definido
```

---

*Referencia general: Software-Design-and-Architecture.md, System-Design.md, Api-Design.md, Computer-Science.md (Theory/)*