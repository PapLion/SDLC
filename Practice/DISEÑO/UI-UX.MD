# UI-UX.md — Diseño de Interfaz y Experiencia

> Este documento define el sistema de diseño y la experiencia del usuario.
> Se completa DESPUÉS de CLIENT-REQUIREMENTS.md y ANTES de implementar.
> El agente consulta este archivo para mantener consistencia visual total.
>
> Adaptar según el tipo de interfaz del proyecto:
> → Web app / dashboard → completar secciones 1-7
> → API sin UI → completar solo secciones 5-7 (UX de errores y respuestas)
> → Bot / CLI → adaptar paleta y lenguaje al contexto

---

## 1. Design System — Paleta de Colores

Definir los colores del sistema con su semántica. Consistencia total: el usuario identifica el tipo de mensaje por el color antes de leer.

```
PRIMARIOS (brand)
Primary:     #[hex]   — acción principal, CTAs, links
Secondary:   #[hex]   — elementos secundarios, hover states
Background:  #[hex]   — fondo de la app
Surface:     #[hex]   — cards, modales, paneles

SEMÁNTICOS (estado)
Success:     #[hex]   — operación exitosa, confirmación
Error:       #[hex]   — error, acción destructiva, alerta crítica
Warning:     #[hex]   — advertencia, atención requerida
Info:        #[hex]   — información neutral, ayuda

TEXTO
Text Primary:   #[hex]   — texto principal
Text Secondary: #[hex]   — texto de apoyo, labels
Text Disabled:  #[hex]   — estado deshabilitado
Text On Dark:   #[hex]   — texto sobre fondos oscuros
```

**Regla:** Los colores semánticos son constantes. `error` siempre es `error`, sin importar el contexto.

---

## 2. Design System — Tipografía

```
Font Family:    [nombre — ej: Inter, sistema]
Font Scale:
  xs:   12px / 0.75rem  — labels pequeños, timestamps
  sm:   14px / 0.875rem — body secundario, captions
  base: 16px / 1rem     — body principal
  lg:   18px / 1.125rem — subtítulos
  xl:   20px / 1.25rem  — títulos de sección
  2xl:  24px / 1.5rem   — títulos de página
  3xl:  30px / 1.875rem — hero, display

Font Weight:
  Regular: 400  — body
  Medium:  500  — labels, elementos interactivos
  Bold:    700  — títulos, énfasis
```

---

## 3. Design System — Componentes Base

Para cada componente definir los estados visuales. La IA implementa según estos estados, no inventa otros.

### Botones
```
Primary:    fondo Primary, texto blanco — acción principal
Secondary:  borde Primary, texto Primary — acción alternativa
Destructive: fondo Error, texto blanco — acciones irreversibles
Ghost:      transparente, texto Primary — acciones terciarias

Estados: default | hover | active | disabled | loading
```

### Inputs y Formularios
```
Default:  borde gris, fondo blanco
Focus:    borde Primary, sin outline nativo
Error:    borde Error + mensaje de error debajo
Disabled: fondo gris claro, cursor not-allowed
Success:  borde Success (cuando aplica validación inline)
```

### Feedback al Usuario
```
Toast / Notificación:
  Success: ícono ✓, fondo Success tono claro, texto Success oscuro
  Error:   ícono ✗, fondo Error tono claro, texto Error oscuro
  Warning: ícono !, fondo Warning tono claro
  Info:    ícono i, fondo Info tono claro

Loading:
  Spinner: [definir si es global, inline, o skeleton]
  Skeleton: preferible para contenido que tarda > 300ms

Empty States:
  Ilustración (o ícono) + título + descripción + CTA (si aplica)
  Cada pantalla vacía explica qué hacer para poblarla
```

### Mensajes de Error
```
REGLA: Todo mensaje de error incluye qué hacer a continuación.
El usuario nunca queda sin una acción clara.

Formato:
  Título: [qué salió mal — claro y directo]
  Descripción: [por qué ocurrió — opcional si es obvio]
  Acción: [qué hacer — botón o instrucción]
```

---

## 4. Flujos de UX — Pantallas Principales

Documentar el flujo completo de cada caso de uso principal. Mensaje por mensaje, pantalla por pantalla.

### Flujo 1 — [Nombre del flujo principal]

```
[Estado inicial]
  ↓ [acción del usuario]
[Estado siguiente]
  ↓ [acción]
[Estado final exitoso]

[Flujo de error]
  ↓ [condición de error]
[Estado de error con mensaje + acción]
```

### Flujo 2 — [Nombre]
```
[documentar de la misma manera]
```

> Completar un flujo por cada caso de uso definido en CLIENT-REQUIREMENTS.md

---

## 5. Lenguaje Visual y Tono

```
Tono general:  [formal / amigable / técnico / casual]
               Ejemplos del proyecto: [escribir 2-3 ejemplos reales]

Mensajes de éxito:   [cómo suena — ej: "¡Listo! Tu pago fue procesado."]
Mensajes de error:   [cómo suena — ej: "No pudimos procesar el pago. Intenta de nuevo."]
Mensajes de carga:   [cómo suena — ej: "Procesando..."]
CTAs:                [cómo suena — ej: "Confirmar" no "Hacer click aquí"]

Reglas de lenguaje:
→ [ej: No usar jerga técnica en mensajes de usuario]
→ [ej: Verbos en imperativo para CTAs]
→ [ej: Siempre en [idioma], sin mezclar]
```

---

## 6. Reglas UX Generales

```
REGLA 1 — Feedback inmediato
→ Toda acción del usuario recibe respuesta visual en < 100ms
→ Si hay procesamiento largo: mostrar loading inmediatamente
→ No dejar al usuario sin feedback nunca

REGLA 2 — Siempre hay una acción siguiente
→ Cada mensaje de error incluye qué hacer
→ Cada estado vacío incluye cómo poblarlo
→ El usuario nunca queda atrapado sin salida

REGLA 3 — Consistencia total
→ Un color = un significado, siempre
→ Un componente = mismos estados, siempre
→ El design system es la única fuente de estilos

REGLA 4 — Jerarquía visual clara
→ Una sola acción primaria por pantalla
→ El CTA principal es visualmente dominante
→ Lo secundario es visualmente secundario

REGLA 5 — Accesibilidad mínima
→ Contraste de texto: mínimo 4.5:1 (WCAG AA)
→ Focus visible en todos los elementos interactivos
→ No depender solo del color para comunicar estado
→ Labels en todos los inputs (no solo placeholders)

[Agregar reglas específicas del proyecto]
```

---

## 7. Responsive y Breakpoints (si aplica)

```
Mobile:   < 640px   → [comportamiento específico del proyecto]
Tablet:   640-1024px → [comportamiento]
Desktop:  > 1024px  → [comportamiento]

Comportamiento en mobile:
→ [ej: navegación pasa a bottom bar]
→ [ej: tablas colapsan a cards]
→ [ej: sidebar se convierte en drawer]
```

---

## 8. Componente Principal — Referencia Completa

Si el proyecto tiene un componente central (tabla, formulario complejo, canvas), documentarlo aquí con todos sus estados y variantes.

```
[ComponentePrincipal]
  Props requeridas: [listar]
  Props opcionales: [listar]
  Estados: [loading, empty, error, populated]
  Variantes: [si las hay]
  Eventos que emite: [onClick, onChange, etc.]
```

---

## 9. UX de la API (si el proyecto tiene API pública o interna)

Aunque no haya UI, la API tiene UX. Consistencia en respuestas.

### Respuestas de éxito
```json
// GET /resource
{ "data": { ... }, "meta": { "total": 100 } }

// POST /resource (creación)
// Status 201
{ "data": { "id": "...", ... } }

// DELETE /resource (eliminación)
// Status 204, sin body
```

### Respuestas de error
```json
// Formato estándar para todos los errores (RFC 7807)
{
  "type":   "https://errors.miapp.com/not-found",
  "title":  "Recurso no encontrado",
  "status": 404,
  "detail": "El usuario con id '123' no existe",
  "instance": "/api/users/123"
}
```

### Códigos de estado consistentes
```
200 → éxito, retorna data
201 → creado exitosamente
204 → eliminado, sin content
400 → input inválido (error del cliente)
401 → no autenticado
403 → autenticado pero sin permisos
404 → recurso no encontrado
409 → conflicto (ej: email ya existe)
422 → entidad no procesable (validación fallida)
429 → rate limit
500 → error interno del servidor
```

> Referencia: Api-Design.md (Theory/) — sección error handling, RFC 7807

---

## 10. Checklist UI/UX — Verificación

```
□ Paleta de colores definida con semántica clara
□ Tipografía y escala definidas
□ Componentes base con estados documentados
□ Flujos principales documentados pantalla a pantalla
□ Mensajes de error con acción siguiente siempre
□ Tono y lenguaje definidos con ejemplos
□ Reglas UX generales aplicables al proyecto
□ Accesibilidad mínima considerada
□ UX de API documentada (si aplica)
```

---

*Referencia: Frontend.md, Frontend-Performance.md (Theory/) para proyectos web*
*Referencia: Api-Design.md, Technical-Writer.md (Theory/) para proyectos API*