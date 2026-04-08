# AI & General Development System (SDLC)

> **Mi sistema personalizado de desarrollo de software con IA como orquestador central**

## 🎯 Propósito

Este repositorio contiene mi metodología completa de **Spec-Driven Development** y el framework de trabajo con IA que aplico en todos mis proyectos. No es código ejecutable — es la infraestructura conceptual y operativa que orquesta cómo trabajo con modelos de IA.

## 📋 Contenido

### `AGENTS.md`
**El corazón del sistema.** System prompt persistente que define:
- Mi filosofía de desarrollo (Ingeniería de Software con IA, no vibe coding)
- Stack técnico completo (TypeScript, Python, Lua, React, Next.js, etc.)
- SDLC personalizado de 7 fases con roles de IA
- Estándares de código y convenciones
- Flujo de trabajo con memoria persistente (Engram)
- Reglas de comportamiento para todos los agentes que trabajan conmigo

### `Metodology/`, `Practice/`, `Theory/`
Documentación estructurada del proceso de desarrollo en 3 dimensiones:
- **Theory**: Fundamentos conceptuales y arquitectónicos
- **Metodology**: Procesos y workflows formales
- **Practice**: Guías prácticas y patrones de implementación

### `skills/` & `skills-lock.json`
Catálogo de skills activas para GitHub Copilot, cubriendo:
- UI/UX (React, Next.js, Tailwind, animaciones)
- Testing (TDD, Playwright, pytest)
- Spec-Driven Development (SDD agents)
- Roblox/Lua game development
- Frameworks modernos (AI SDK v5, Zod 4, Zustand 5)

### `mcp-config.json`
Configuración de MCPs (Model Context Protocol) activos:
- `playwright` — E2E testing
- `browser-use` — Navegación web agéntica
- `context7` — Documentación actualizada de librerías
- `engram` — Memoria persistente RAG
- `roblox-studio` — Desarrollo Roblox
- `github-mcp-server`, `ide` — Integraciones

## 🔄 Flujo de Trabajo

```
CLIENT TEXT → CLIENT-REQS → GAPS → RESOLVER-GAPS → ARCHITECT 
    → UI-UX + TECHLEAD → IMPLEMENT → QA → DEPLOY
         ↑                                      |
         └─── Feedback Loop (Engram) ──────────┘
```

**Principio fundamental:** Arquitectura primero, código después.

## 🤖 Agentes y Modelos

| Rol | Modelo | Uso |
|-----|--------|-----|
| Orquestador / Arquitecto | Claude Sonnet 4.6 | Diseño, decisiones técnicas, este repo |
| Implementación | GitHub Copilot | Código, tests, CI/CD, refactors |
| Razonamiento rápido | GPT-5 mini | Tareas sin contexto de proyecto |

## 🛡️ Herramientas Clave

- **GGA (Gentleman Guardian Angel)** — Pre-commit hook que valida contra `AGENTS.md`
- **OpenSpec** — SDD via slash commands (`/opsx:propose`, `/opsx:apply`, `/opsx:archive`)
- **Engram** — Sistema de memoria persistente entre sesiones
- **GitHub Actions** — CI/CD pipeline

## 🚀 Cómo Usar Este Sistema

1. **Para proyectos nuevos**: Empieza con `CLIENT-REQUERIMENTS.md` en fase de análisis
2. **Para IA que trabaja conmigo**: Lee `AGENTS.md` completo antes de actuar
3. **Para consulta**: Usa la estructura de carpetas para encontrar guías específicas
4. **Para evolución**: Los docs son "spec anchored" — evolucionan con la práctica

## 📚 Stack Técnico Principal

- **Frontend**: React 19, Next.js 15, Tailwind CSS 4, Zustand 5, Zod 4
- **Backend**: Next.js API Routes, Django DRF, FastAPI
- **AI**: AI SDK v5 (Vercel), Claude, GPT, Gemini
- **Testing**: Vitest, Playwright, pytest
- **DB**: PostgreSQL, SQLite
- **DevOps**: Git, GitHub Actions, Docker
- **Game Dev**: Roblox Studio / Lua

## ✅ Estándares

- **Commits**: Conventional Commits (`feat:`, `fix:`, `chore:`, etc.)
- **PRs**: Siempre via Pull Request, nunca push directo a `main`
- **Testing**: TDD para lógica crítica (auth, negocio, integraciones)
- **Code Style**: TypeScript strict, Python type hints, ESLint + Prettier / Ruff

## 🧠 Filosofía

> "Crear software ≠ programar"  
> "La IA no me domina, yo la orquesto"  
> "El humano es el Validador de Intención, el código es el artefacto derivado"

---

##  El Problema del Loop Infinito (resumen desde HITL)

El loop infinito de "revisá de nuevo, encontré algo más" ocurre porque **el modelo busca en un espacio infinito de posibles problemas**. Sin criterios formales de "completo", el proceso no termina nunca.

La solución es documentos firmados con criterios de completitud medibles:

```
Sin firma → espacio infinito → loop eterno
Con firma → espacio cerrado → proceso determinista
```

**Regla:** Después de la firma, si la IA "encuentra algo nuevo", no entra al documento actual. Va a la siguiente versión con fecha y trazabilidad. La versión firmada es inmutable.

**Criterio de cierre del SDLC como sistema:** El SDLC está completo contra el modelo de 7 fases con roles. Cualquier cosa que la IA "encuentre" que no tenga fuente en ese modelo no es un gap real — es opinión. Ver `AGENTS.md` sección SDLC para el mapeo completo de fases.


**Versión**: 1.0  
**Autor**: Dani  
**Última actualización**: Marzo 2026

*Si hay contradicción entre este README y AGENTS.md, AGENTS.md gana.*

## ⚠️ Nota sobre el repositorio SDLC (documentación canónica)

Este repositorio es el repositorio base del SDLC. A diferencia de proyectos producto donde se suele ignorar la carpeta de procesos, aquí las guías y documentos del proceso se versionan y se mantienen como fuente oficial. Sigue las pautas de `GITIGNORE-GUIDE.md` para proyectos, pero en este repositorio:
- No ignores los documentos de SDLC: `Practice/`, `Metodology/`, `Theory/`, `AGENTS.md`, `HITL.md`, `skills/`, etc., deben estar versionados.
- El `GITIGNORE-GUIDE.md` explica la convención general; este repositorio contiene la excepción (documentación canónica).

## Cambios recientes (2026-04-08)
- **docs:** Added `Practice/IMPLEMENTACION/PR.MD` — Pull Request workflow guide.
- **docs:** Updated `HITL.md` — Human-in-the-Loop principles.
- **docs:** Updated `AGENTS.md` and various design docs under `Practice/DISEÑO/`.
- **meta:** Alineado README con el estado actual del repositorio y las reglas de versionado de SDLC.

---

