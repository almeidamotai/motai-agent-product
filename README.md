# Motai Agent Product

Kit del agente de Claude Code especializado en producción de PRDs para Motai. Este repositorio **no aloja PRDs** — es la plantilla canónica que se clona para arrancar cada proyecto nuevo.

## Uso

Crea el repo de un proyecto nuevo a partir de esta plantilla:

```bash
gh repo create <nombre-proyecto> --template almeidamotai/motai-agent-product --private --clone
```

O desde la interfaz de GitHub: **"Use this template"** en la página de este repo.

Esto deja `CLAUDE.md` y `.claude/commands/` listos en el repo nuevo. Abre Claude Code ahí y los comandos `/start` a `/compile` estarán disponibles.

Cada repo de proyecto aloja un solo PRD (modelo un-repo-por-proyecto): los archivos se generan directamente en `prds/`, sin subcarpeta por feature ni rama dedicada.

## Flujo de trabajo

```
/start → /why → /who → /what → /success → /how → /stories → /risks → /check → /compile
```

| Comando | Propósito | Produce |
|---------|-----------|---------|
| `/start` | Caracteriza el feature y selecciona el template | `prds/00-context.md` |
| `/why` | Extrae el problema, JTBD y evidencia | `prds/01-why.md` |
| `/who` | Define segmentos de usuario y segmento primario | `prds/02-who.md` |
| `/what` | Define solución, capacidades y fuera de alcance | `prds/03-what.md` |
| `/success` | Define métricas SMART con baseline y guardrails | `prds/04-success.md` |
| `/how` | Escribe REQs verificables y reglas de negocio con tablas | `prds/05-how.md` |
| `/stories` | Escribe historias de usuario con Gherkin completo | `prds/06-stories.md` |
| `/risks` | Consolida riesgos, dependencias y criterios go/no-go | `prds/07-risks.md` |
| `/check` | Audita coherencia global y certifica readiness | `prds/check-report.md` |
| `/compile` | Ensambla el PRD final | `prds/prd.md` |

`/clarify` corre automáticamente al final de cada comando de contenido — no necesitas invocarlo.

### Templates de PRD

Seleccionados automáticamente por `/start` según complejidad del feature:

- **Lean** — features simples, < 1 semana de desarrollo
- **Integral** — features estándar, 1–4 semanas
- **Amazon PR/FAQ** — features nuevos o exploración de producto
- **Google PRD** — iniciativas estratégicas con múltiples equipos

### Git

Los commits siguen la convención `prd([comando]): descripción [flags: N]`. El `git log` del repo del proyecto es una historia legible del proceso de especificación.

## Mantenimiento de esta plantilla

Este repo solo debe cambiar cuando cambian los comandos o el contexto de dominio:

- **`CLAUDE.md`** — contexto de dominio Motai (sistemas, roles, regulación, glosario). Edítalo cuando el negocio cambie.
- **`.claude/commands/*.md`** — los 11 comandos del proceso. Edítalos cuando el proceso de especificación cambie.

Los repos de proyecto ya creados a partir de esta plantilla **no se actualizan automáticamente**. Si `CLAUDE.md` o los comandos cambian aquí, hay que volver a copiar los archivos actualizados a los repos de proyecto activos.
