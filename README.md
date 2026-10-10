# Plazos.co

SaaS para la gestión inteligente de derechos de petición, tutelas y desacatos en empresas y entidades colombianas.

## Problema

Las empresas que reciben tutelas, derechos de petición y desacatos deben responder en plazos estrictos definidos por la ley colombiana. Incumplir un plazo puede generar sanciones, desacatos e incluso consecuencias penales. Hoy muchas organizaciones gestionan estos requerimientos en hojas de cálculo o correo electrónico, lo que genera errores humanos y respuestas tardías.

## Solución

Plazos.co automatiza la clasificación, el cálculo de plazos en días hábiles y el seguimiento de cada requerimiento:

- **Clasificación con IA:** al subir un documento PDF, la IA extrae el tipo (tutela, derecho de petición, desacato), las partes, el radicado y la fecha de notificación.
- **Motor de plazos:** calcula la fecha límite en días hábiles según la legislación colombiana (Decreto 2591 de 1991, Ley 1755 de 2015), descontando fines de semana y festivos.
- **Bandeja con semáforo:** muestra los requerimientos ordenados por urgencia (rojo, naranja, amarillo, verde).
- **Alertas escalonadas:** notificaciones automáticas a 5, 2 y 1 días hábiles del vencimiento, con escalamiento al coordinador si no hay respuesta.
- **Bitácora de auditoría:** registro inmutable de cada acción para trazabilidad.

## Stack

| Capa | Tecnología |
|---|---|
| Backend | Python 3.12+, FastAPI, SQLAlchemy 2, Alembic |
| Frontend | Next.js (App Router), TypeScript, Tailwind CSS, shadcn/ui |
| Base de datos | PostgreSQL, Redis |
| IA | API de Claude (extracción y clasificación estructurada) |
| Infraestructura | Docker Compose, GitHub Actions |

## Estructura del repositorio

```
plazos-co/
├── backend/          # API en FastAPI
├── frontend/         # App en Next.js
├── docs/             # Arquitectura, ADRs, specs
├── docker/           # Dockerfiles y docker-compose
├── .github/          # Workflows de CI/CD
└── scripts/          # Utilidades (seeds, migraciones, etc.)
```

## Estado del proyecto

En desarrollo — Semana 1 (análisis).

## Licencia

MIT — ver [LICENSE](LICENSE).
