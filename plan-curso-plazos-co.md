# Plan del curso — Plazos.co (proyecto de portafolio)

Alumno: William Moya Santana · Profesor: Claude · Inicio: 5 de octubre de 2026
Dedicación: 5–10 h/semana → unas 10 semanas hasta el despliegue.
Herramientas: Warp + Claude Code (código), GitHub (versiones), NotebookLM (estudio e investigación).

---

## 1. El proyecto

**Plazos.co** (nombre provisional): SaaS para que **empresas y entidades** gestionen los derechos de petición, tutelas y desacatos que reciben. La IA clasifica cada documento y lo enruta al área responsable; el sistema calcula los plazos en días hábiles, prioriza con un semáforo, envía alertas escalonadas y deja una bitácora de auditoría. Los abogados independientes y la consulta automática de procesos quedan para la v2.

> El alcance detallado está en **`claude/mvp-alcance-v1.md`** (actualizado el 8 de octubre de 2026 con la retroalimentación del abogado).

| Módulo | Qué demuestra en tu hoja de vida |
|---|---|
| Organizaciones (multi-tenant), áreas, usuarios y roles | Autenticación, autorización, modelado SaaS |
| Requerimientos + motor de reglas de plazos (días hábiles de Colombia) | Reglas de negocio configurables, algoritmos, TDD |
| IA: extracción, clasificación y enrutamiento por área | LLM con salida estructurada, human-in-the-loop, colas de trabajo |
| Bandeja con semáforo + panel | Frontend con datos, UX |
| Alertas escalonadas + escalamiento | Tareas programadas, correo |
| Bitácora de auditoría + vinculación petición → tutela → desacato | Trazabilidad, diseño orientado a eventos |
| Conectores (interfaz preparada) | Arquitectura extensible (patrón adapter) |

**Regla de oro sobre datos:** usa solo documentos **sintéticos o anonimizados** que inventemos nosotros. Nunca uses documentos reales de trabajos anteriores (Ley 1581 de 2012, habeas data).

## 2. Stack

- **Backend:** Python 3.12+, FastAPI, Pydantic v2, SQLAlchemy 2 + Alembic, pytest, ruff, uv.
- **Base de datos:** PostgreSQL (+ pgvector si llegamos a la búsqueda semántica). Redis para la cola de trabajos.
- **IA:** API de Claude para extraer y clasificar con salida estructurada.
- **Frontend:** Next.js (App Router) + TypeScript + Tailwind + shadcn/ui, TanStack Query, Vitest y Playwright.
- **Infraestructura:** Docker Compose en local, GitHub Actions (lint, tests, build), Sentry para errores.
- **Despliegue (a definir en la semana 9):** frontend en Vercel; backend en Railway, Render o Fly.io; base de datos en Neon o Supabase.

## 3. Cómo usamos GitHub

- **GitHub Flow:** `main` siempre funciona. Cada cambio va en una rama (`feat/...`, `fix/...`), se sube con un Pull Request y se fusiona.
- **Conventional Commits:** `feat:`, `fix:`, `docs:`, `test:`, `refactor:`, `chore:`.
- **Issues y Projects:** cada spec es un issue y vive en un tablero Kanban.
- **Protección de `main`:** solo se fusiona si la CI pasa.
- **Documentación:** README con capturas o GIF, `docs/` con la arquitectura y ADRs (registros de decisiones).

## 4. De OpenCode (los apuntes) a Claude Code (tu herramienta)

| Apuntes (OpenCode) | Claude Code |
|---|---|
| `AGENTS.md` | `CLAUDE.md`. Dentro escribes `@AGENTS.md` para que lea las dos (así sirve para cualquier agente) |
| `/init` | `/init` (genera `CLAUDE.md`) |
| TAB / Shift+TAB entre Plan y Build | **Shift+Tab** pasa al modo plan |
| `.opencode/commands/x.md` | `.claude/commands/x.md` (usa `$ARGUMENTS`, `$1`, `$2`) |
| `.agents/skills/x/SKILL.md` | `.claude/skills/x/SKILL.md` |
| `opencode.json` → mcp | `claude mcp add ...` o el archivo `.mcp.json` en la raíz |
| `/mcps` | `/mcp` |
| `.opencode/agents/x.md` (`mode: subagent`) | `.claude/agents/x.md` (frontmatter: `name`, `description`, `tools`, `model`) |
| permisos del agente | `.claude/settings.json` (allow/deny) + `tools:` de cada subagente |
| `/undo` | `/rewind` (o Esc dos veces) |
| `/compact`, `@archivo`, `!comando` | Igual |

## 5. Ruta del curso (ingeniería aplicada + herramientas de los PDF)

| Semana | Fase | Qué construimos | Herramientas del curso |
|---|---|---|---|
| 0 | Preparación | Repo, Warp + Claude Code, cuaderno de NotebookLM | NotebookLM, Git |
| 1 | **Análisis** | ✅ Validación con el abogado y alcance del MVP v1. Faltan: historias de usuario, requisitos en EARS y `constitution.md` | Prompt engineering, NotebookLM (dominio legal) |
| 2 | **Diseño** | Arquitectura (C4), diagrama entidad-relación, contrato de la API (OpenAPI), wireframes de la bandeja, ADRs (incluida la interfaz de conectores) | Modo plan |
| 3 | Arnés | `CLAUDE.md`/`AGENTS.md`, `MEMORY.md`, comandos `/sdd-*`, skills, MCPs (Context7, GitHub, Playwright o Chrome DevTools); monorepo, Docker y CI | Harness engineering, MCP |
| 4 | **Desarrollo** | Spec 001: organizaciones, áreas, usuarios y roles | SDD completo |
| 5 | Desarrollo | Spec 002: requerimientos + reglas de plazos + días hábiles + bandeja con semáforo (TDD fuerte) | Skill `dias-habiles-co` |
| 6 | Desarrollo | Spec 003: carga de PDF + IA (extracción, clasificación, área sugerida) + confirmación humana | Context7, revisión de seguridad |
| 7 | Desarrollo | Spec 004: alertas escalonadas + escalamiento + bitácora de auditoría | Chrome DevTools/Playwright para validar la interfaz |
| 8 | Desarrollo | Spec 005: vinculación petición → tutela → desacato + panel (+ búsqueda semántica si da el tiempo) | Multiagente: coordinador, planificador, implementador y revisor |
| 9 | **Implementación** | Despliegue, variables de entorno, Sentry, OWASP básico, organización demo con datos ficticios | GitHub Actions (CD) |
| 10 | Cierre | README con GIF, video demo, post en LinkedIn, viñeta para el CV | NotebookLM para preparar entrevistas |

Cada spec sigue el flujo de los apuntes del día 2: constitución → spec → clarificación → plan → tareas → implementación (tests primero) → validación.

## 6. NotebookLM: para qué y cómo

**Qué es:** el cuaderno de investigación de Google (notebooklm.google.com). Le subes fuentes (PDF, Google Docs, páginas web, videos de YouTube, texto pegado) y te responde **solo con base en esas fuentes, con citas** que puedes revisar. Además genera resúmenes de audio y video, mapas mentales, guías de estudio, tarjetas de memoria y cuestionarios.

**La división del trabajo:** NotebookLM es para que **tú** aprendas e investigues. Claude Code es para que el **agente** construya. Lo que aprendas en NotebookLM lo conviertes en specs y en archivos de `docs/`.

**Cuadernos del curso:**
1. **"Curso Desarrollo con IA":** los 3 PDF de apuntes. Úsalo para repasar, escuchar el resumen de audio y hacerte cuestionarios.
2. **"Dominio Plazos.co":** Decreto 2591 de 1991, Ley 1755 de 2015, la ley de festivos (Ley Emiliani) y guías públicas de PQRSD de entidades. Úsalo para validar la tabla de plazos.
3. **"Plazos.co – decisiones":** tus specs y ADRs a medida que los escribas. Te sirve para estudiar tu propio proyecto antes de una entrevista.

**Paso a paso:**
1. Entra a notebooklm.google.com con tu cuenta de Google y crea un cuaderno nuevo.
2. Usa "Añadir fuentes" y sube los PDF o pega enlaces.
3. En el chat, haz preguntas concretas y abre las citas numeradas para verificar.
4. En el panel de herramientas (Studio), genera el resumen de audio, el mapa mental, la guía de estudio o el cuestionario.
5. Guarda las respuestas útiles como notas. Las conclusiones las pasas a tu repo en `docs/research/`.

**Prompts útiles:**
- "Explícame la diferencia entre comandos, skills y subagentes con un ejemplo de cada uno, citando los apuntes."
- "Hazme un cuestionario de 10 preguntas sobre SDD y EARS."
- "Según la Ley 1755 y el Decreto 2591, haz una tabla de plazos con su artículo de origen."

**Límites:** solo sabe lo que hay en tus fuentes. Revisa siempre las citas y no subas documentos confidenciales.

## 7. Forma de trabajar conmigo

- Cada clase tiene: teoría corta, práctica en tu repo y una tarea con criterio de "hecho cuando".
- Tú tomas las decisiones de arquitectura y yo las cuestiono. La IA ejecuta, pero el responsable eres tú.
- Al terminar cada sesión actualizamos `MEMORY.md` y este plan.

## 8. Tareas pendientes

**Lección 1 (si aún no la terminas):**
1. **NotebookLM:** cuaderno "Curso Desarrollo con IA" con los 3 PDF, resumen de audio y cuestionario de 10 preguntas.
2. **Claude Code en Warp:** `claude --version`, `/status`, `/help` y modo plan (Shift+Tab).
3. **GitHub:** repo público `plazos-co` con README, licencia MIT y `.gitignore`; primer commit `docs: initial readme`.

**Lección 2 (nueva):**
4. Pásale a tu amigo las 4 preguntas de la sección 9 de `mvp-alcance-v1.md`, sobre todo que valide la tabla de plazos.
   - Hecho cuando: tengas la tabla corregida o confirmada.
5. Crea el cuaderno "Dominio Plazos.co" en NotebookLM con la Ley 1755 de 2015 y el Decreto 2591 de 1991, y contrasta la tabla de plazos.
   - Hecho cuando: cada fila de la tabla tenga su artículo verificado con una cita.

**Próxima clase:** historias de usuario + requisitos en EARS + `docs/constitution.md`.
