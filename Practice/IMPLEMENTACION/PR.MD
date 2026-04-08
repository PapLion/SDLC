# PR.md — Flujo de Pull Request

> Este archivo es una instrucción directa para el agente de implementación.
> Se lee al inicio de cada sesión de PR. No es negociable.

---

## Pasos obligatorios

Ejecuta en este orden exacto. Sin saltar ninguno.

### 1. Estado del repositorio
- Mostrar staged changes
- Mostrar unstaged/uncommitted changes
- Mostrar rama actual y rama base

### 2. Formatear y limpiar código
- Ejecutar el formateador del proyecto (`npm run format` / `ruff format` / lo que aplique)
- Stage los cambios de formato antes del commit

### 3. Crear commit convencional

Usar el tipo que corresponda:
- `feat:` → nueva feature
- `fix:` → corrección de bug o incompatibilidad
- `refactor:` → refactoring sin cambio de comportamiento
- `test:` → añadir o corregir tests
- `docs:` → documentación
- `chore:` → tareas de mantenimiento, dependencias
- `perf:` → mejora de rendimiento sin cambio funcional

Formato:
```
<tipo>: <descripción corta en inglés, imperativo, sin punto final>

- Detalle 1 — qué cambió y por qué
- Detalle 2
- Detalle 3
```

❌ Nunca usar: "fixed stuff", "updates", "WIP", "changes", "misc"

### 4. Comparar con rama base y generar descripción del PR

- Detectar rama base del proyecto (main / master / develop)
- Generar diff contra la rama base
- Construir descripción con esta estructura:

```markdown
## Summary
<qué cambia y por qué — 2-3 oraciones>

## Technical Details
<detalles de implementación relevantes para el revisor>

## Testing
<cómo se verificó que funciona>
- [ ] Build pasa sin errores
- [ ] Tests corren en verde
- [ ] Validación manual completada (si aplica)

## Related
<referencia al bloque del ROADMAP, issue, o tarea que corresponde>
```

### 5. Push y crear PR
```bash
git push origin <rama-actual>
gh pr create --title "<tipo>: <descripción>" --body "<descripción generada>"
gh pr view --web
```

---

## Convención de ramas

```
<tipo>/<descripción-corta-en-kebab-case>

feat/user-authentication
feat/payment-integration
fix/session-timeout
refactor/database-layer
docs/api-reference
```

La rama base depende del proyecto — verificar TECHLEAD.md o ROADMAP.md para confirmarlo.

---

## Criterios de bloqueo

Un PR no puede mergearse si:
- El build falla
- Los tests existentes están en rojo
- No tiene descripción de qué cambia y por qué
- Tiene secretos o tokens hardcodeados
- Tiene `console.log` / `print` de debugging en código de producción

---

## Uso

Cuando se te pida:
> "Lee PR.md y genera un PR con temática X"

Ejecuta los 5 pasos usando X como la temática del commit.

---

*Referencia: Code_Review_BestPractices.md, Code_Review_Pyramid.md (Theory/)*