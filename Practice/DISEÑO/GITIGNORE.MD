# GITIGNORE-GUIDE.md — Qué entra al repositorio y qué no

> Este documento explica la estrategia de `.gitignore` para todos los proyectos
> que usan esta metodología SDLC.
> Se copia junto con la carpeta `/SDLC/` a cada proyecto nuevo.
>
> **Principio fundamental:** El repositorio es para el producto, no para el proceso.
> La metodología interna no le pertenece al cliente ni al código fuente.

---

## 1. La Decisión Central — Qué hacer con /SDLC/

Cada proyecto copia la carpeta `/SDLC/` completa para que los agentes tengan
contexto. Pero el cliente no necesita ver cómo se construyó su producto.

```
IGNORAR (no pushear):
/SDLC/ANALISIS-REQUISITOS/     → proceso interno, no del producto
/SDLC/DISEÑO/                  → decisiones de proceso, no del código

VERSIONAR (sí pushear):
/SDLC/ANALISIS-REQUISITOS/ARCHITECH.md   → útil para quien mantenga el código
/SDLC/DISEÑO/TECHLEAD.md                 → stack y convenciones del proyecto
/SDLC/DISEÑO/DATABASE.md                 → schema y decisiones de DB
```

Regla simple: si un dev externo que toma el proyecto en el futuro lo necesita
para entender el código → entra al repo. Si es proceso de construcción → no entra.

---

## 2. Categorías de Archivos a Ignorar

### Proceso SDLC interno
```
# Docs de proceso — no del producto
SDLC/ANALISIS-REQUISITOS/CLIENT-REQUIREMENTS*.md
SDLC/ANALISIS-REQUISITOS/GAPS-SDLC*.md
SDLC/ANALISIS-REQUISITOS/RESOLVER-GAPS*.md
SDLC/ANALISIS-REQUISITOS/TECH-DEBT*.md
SDLC/DISEÑO/QA*.md
SDLC/SCOPE-MANAGEMENT-LOG.md
BACKLOG*.md
plan.md                  # generado por Copilot antes de implementar
```

### Residuos de Git y herramientas
```
commit_msg.txt           # archivo temporal de Git — nunca debería pushearse
*.orig                   # archivos de merge conflict sin resolver
.git/                    # nunca — Git lo maneja solo, pero por si acaso
```

### Entorno y secrets
```
.env
.env.local
.env.development
.env.staging
.env.production
# Excepción: .env.example SÍ se pushea — es la fuente de verdad de variables
```

### Bases de datos locales
```
*.db
*.sqlite
*.sqlite3
/data/
/backups/
```

### Logs
```
/logs/
*.log
npm-debug.log*
yarn-debug.log*
```

### Dependencias
```
node_modules/
__pycache__/
*.pyc
.venv/
venv/
dist/
build/
.next/
out/
```

### Sistema operativo
```
.DS_Store
Thumbs.db
desktop.ini
```

### IDEs y editores
```
.vscode/settings.json    # settings personales — sí pushear extensions.json
.idea/
*.swp
*.swo
```

---

## 3. Qué SÍ se pushea siempre

```
.env.example             → fuente de verdad de variables (sin valores)
.gitignore               → este archivo
README.md                → instrucciones de setup
AGENTS.md                → si el repo es colaborativo con IA
SDLC/ANALISIS-REQUISITOS/ARCHITECH.md
SDLC/DISEÑO/TECHLEAD.md
SDLC/DISEÑO/DATABASE.md  → si el proyecto tiene DB
```

---

## 4. Cómo usar este sistema en un proyecto nuevo

```bash
# 1. Copiar la carpeta SDLC al proyecto nuevo
cp -r /tu-repo-sdlc/SDLC ./SDLC

# 2. Copiar el .gitignore base
cp /tu-repo-sdlc/.gitignore.base ./.gitignore

# 3. Añadir las exclusiones específicas del stack del proyecto
#    (ver sección 5 de este doc)

# 4. Verificar antes del primer commit
git status                    # ver qué ve Git
git check-ignore -v SDLC/     # verificar que SDLC está ignorado correctamente
```

---

## 5. Extensiones por Stack

Añadir al `.gitignore` base según el stack del proyecto.

### Next.js / React
```
.next/
out/
.vercel/
```

### Python / Django / FastAPI
```
__pycache__/
*.pyc
*.pyo
.venv/
venv/
*.egg-info/
dist/
htmlcov/
.coverage
```

### Discord Bot (Node.js)
```
*.db                     # games.db y similares
/data/
/backups/
pm2.log
ecosystem.config.js.bak
```

### Roblox (si el proyecto tiene scripts locales)
```
*.rbxl.lock
/roblox-studio-output/
```

---

*Versión: 1.0 | 2026-04-07*
*Posición SDLC: Transversal — se copia en cada proyecto nuevo junto con /SDLC/*