# QA.md — Estrategia de Pruebas

> Estrategia liviana pero real.
> No buscamos cobertura perfecta — buscamos que lo crítico no falle.
> Completar este documento ANTES de implementar, junto con TECHLEAD.md.

---

## 1. Roles en QA — Quién hace qué

```
Agente de implementación (OpenCode / Copilot / Cursor)
→ Escribe unit tests mientras implementa cada módulo
→ Corre el test suite completo antes de mergear cada bloque
→ Corrige los errores que los tests revelan

Agente de revisión (Kimi / segundo agente)
→ Revisión de código post-implementación por bloque
→ Detecta errores de lógica sutiles
→ Verifica que los patrones de ARCHITECH.md se implementaron correctamente

Agente de fix puntual (Cursor)
→ Fix rápido de errores específicos que la revisión reporta
→ Refactoring puntual cuando algo no cumple estándares

Yo (Validador humano — HITL)
→ Prueba manual del flujo principal al finalizar cada bloque
→ Valida que la experiencia real coincide con CLIENT-REQUIREMENTS.md
→ Aprueba o rechaza cada bloque antes de continuar
```

---

## 2. Scope — Qué Testeamos y Qué No

### ✅ Testeamos siempre
```
→ Lógica de dominio pura — reglas de negocio, cálculos, validaciones
→ Capa de datos — queries, transacciones, migraciones
→ Casos críticos de error — fallos de red, DB caída, race conditions
→ Flujos principales end-to-end de los casos de uso del cliente
→ Validaciones de seguridad — permisos, autenticación, inputs
→ Edge cases identificados en GAPS-SDLC.md
```

### ❌ No testeamos (al menos en primera iteración)
```
→ Librerías de terceros — ya tienen sus propios tests
→ UI puramente visual — se valida manualmente
→ Funcionalidades de bajo riesgo y bajo uso
→ 100% de cobertura — no es el objetivo
```

---

## 3. Tipos de Prueba por Capa

```
DOMAIN LAYER → Unit Tests
  → Lógica pura, sin frameworks, sin DB
  → Rápidos, aislados, muchos
  → El agente los escribe al implementar cada clase/función
  → Herramienta: [Vitest / pytest / Jest]

INFRASTRUCTURE LAYER → Integration Tests
  → DB con instancia real en memoria o test container
  → Cache con datos reales
  → Servicios externos con mocks controlados
  → Herramienta: [Vitest / pytest]

APPLICATION LAYER → Integration Tests
  → Orquestadores con mocks de servicios externos
  → Middleware en secuencia real
  → Flujos end-to-end dentro del backend
  → Herramienta: [Vitest / pytest]

PRESENTATION LAYER → Manual (yo)
  → UX real, flujos de usuario, respuestas visuales
  → Estados de error visibles para el usuario
  → Consistencia con CLIENT-REQUIREMENTS.md
```

---

## 4. Casos Críticos — Nunca Pueden Fallar

Identificar desde el diseño los escenarios que, si fallan, bloquean el proyecto.
Si alguno falla en QA = bloqueante para mergear el bloque.

### 🔴 Crítico 1 — [Nombre del caso]
```
Test: [descripción de cómo se reproduce]
Esperado: [qué debe pasar]
Herramienta: [manual / unit test / integration test]
Bloque ROADMAP: [número de bloque]
```

### 🔴 Crítico 2 — [Nombre del caso]
```
Test: [descripción]
Esperado: [resultado]
Herramienta: [herramienta]
Bloque ROADMAP: [bloque]
```

> Completar un caso crítico por cada área de alto riesgo identificada en RESOLVER-GAPS.md.

**Áreas de riesgo típicas según tipo de proyecto:**
```
Sistemas con pagos:    → doble cobro, monto incorrecto, fallo de gateway
Sistemas con auth:     → acceso sin autenticación, privilege escalation
Sistemas con archivos: → path traversal, archivos corruptos, size overflow
Sistemas con tiempo:   → race conditions, timezone bugs, TTL incorrecto
Sistemas con roles:    → usuario ejecuta acción de admin
Sistemas con cache:    → cache stale entrega datos incorrectos
```

---

## 5. Unit Tests por Módulo

Especificar los escenarios que el agente debe cubrir al implementar cada módulo.

### [Módulo de dominio principal]
```typescript
// [NombreServicio]
- [método]() retorna [resultado] cuando [condición]
- [método]() lanza [error] cuando [condición]
- [método]() maneja [edge case] correctamente
```

### [Módulo de infraestructura]
```typescript
// [NombreRepository]
- save() persiste correctamente y retorna entidad con id
- findById() retorna null cuando no existe
- [operación crítica] es atómica (usa transacción)
```

### [Módulo de aplicación]
```typescript
// [NombreOrchestrator]
- [flujo principal] completa end-to-end
- [flujo de error] loggea y notifica correctamente
- [flujo concurrente] no produce race condition
```

---

## 6. Fixtures y Datos de Prueba

```typescript
// tests/fixtures/[dominio].ts

// Función que devuelve DB limpia para cada test
export function createTestDb() {
  // [configurar DB en memoria]
}

// Datos de prueba representativos
export const testUsers = {
  standard: { /* ... */ },
  admin:    { /* ... */ },
  new:      { /* ... */ },
}

export const testEntities = {
  valid:    { /* ... */ },
  invalid:  { /* ... */ },
  edge:     { /* ... */ },  // casos límite
}
```

**Regla:** Los tests nunca tocan la DB real del proyecto. Siempre DB en memoria o test container.

---

## 7. Checklist de Validación Manual por Bloque

Yo ejecuto este checklist en el entorno real al finalizar cada bloque.

```
BLOQUE 1 — Fundación
□ [flujo básico funciona end-to-end]
□ [errores muestran mensaje útil, no stack trace]
□ [logs aparecen correctamente]
□ [variables de entorno faltantes causan error de startup claro]

BLOQUE 2 — [Nombre]
□ [validación principal funciona]
□ [caso de error más común manejado correctamente]
□ [integración con bloque anterior no rota]

[Repetir por bloque del ROADMAP]
```

---

## 8. Criterios Bloqueantes vs Menores

### 🔴 Bloqueante — no se puede continuar al siguiente bloque
```
→ El sistema crashea con el flujo principal
→ Un caso crítico (sección 4) falla
→ Datos incorrectos en DB después de una operación
→ Brecha de seguridad en flujo de autenticación
→ Tests en rojo que cubren lógica crítica
```

### 🟡 Menor — se puede continuar, se corrige en bloque de hardening
```
→ Mensaje de error con texto incorrecto
→ Respuesta lenta pero funcional
→ Test que falla por flakiness (timing)
→ Log con nivel incorrecto
→ Comportamiento edge case poco probable
```

---

## 9. Flujo de QA por Bloque

```
Agente termina bloque
        ↓
Corre: npm run test / pytest
        ↓
¿Tests pasan?
    ├── ❌ No → Agente corrige + fix puntual si es error simple
    │          → Segundo agente revisa si es error de lógica complejo
    └── ✅ Sí → Segundo agente hace code review del bloque
                        ↓
               ¿Encuentra issues?
                    ├── ❌ Sí → Fix puntual → re-review
                    └── ✅ No → Yo valido manualmente
                                        ↓
                               ¿Validación manual pasa?
                                    ├── ❌ No → reporto issue → agente corrige
                                    └── ✅ Sí → bloque DONE → siguiente bloque
```

---

## 10. Checklist QA — Antes de Empezar a Implementar

```
□ Roles de agentes definidos
□ Scope claro — qué sí y qué no se testea
□ Tipos de prueba por capa definidos
□ Casos críticos identificados (mínimo 3-5)
□ Unit tests por módulo especificados
□ Fixtures de datos de prueba definidos
□ Checklist de validación manual por bloque
□ Criterios bloqueantes vs menores
□ Flujo de QA por bloque con todas las herramientas
```

---

*Referencia: Code_Review_BestPractices.md, Code_Review_Pyramid.md, Api-Security.md (Theory/)*