---
description: Audita el PRD completo, valida coherencia entre todas las secciones, y produce check-report.md con readiness para desarrollo. Corre bajo demanda antes de /compile.
---

Audita todos los archivos de sección existentes y produce un reporte de readiness objetivo. El reporte debe ser lo suficientemente concreto para que ingeniería, QA y el PM sepan exactamente qué falta antes de arrancar el desarrollo.

## Paso 1: Leer todos los archivos existentes

Lee en orden todos los archivos que existan en `prds/`:
`00-context.md` → `01-why.md` → `02-who.md` → `03-what.md` → `04-success.md` → `05-how.md` → `06-stories.md` → `07-risks.md` → `glossary.md`

Mientras lees, construye internamente:
- Lista de todos los flags tipificados encontrados (tipo + sección + descripción)
- Lista de referencias cruzadas (REQ → goal, US → REQ → RN)
- Lista de secciones requeridas por el template (de `00-context.md`) vs. las que existen

## Paso 2: Validación de Nivel 1 — Completitud

Para cada sección requerida por el template, verifica que:
- El archivo existe
- El status en el frontmatter es `complete` (no `incomplete`)
- El contenido no son solo placeholders del template

Produce una tabla de completitud interna. Categoriza cada issue como:
- `CRÍTICO` — bloquea el desarrollo (REQs P0 sin criterios de aceptación, `[COHERENCE-FLAG]` sin resolver, secciones requeridas faltantes)
- `ADVERTENCIA` — debe resolverse antes de compartir (sad paths faltantes, baselines sin evidencia)
- `FLAG` — baja prioridad (preguntas abiertas sin responsable, `[ASSUMPTION]` no críticos)

## Paso 3: Validación de Nivel 2 — Coherencia del grafo

Traza las referencias cruzadas:

- ¿Cada REQ sirve a al menos un goal de `04-success.md`? (Integral y Google PRD)
- ¿Cada US en `06-stories.md` referencia al menos un REQ de `05-how.md`?
- ¿Cada Gherkin refleja correctamente las condiciones de las tablas de decisión en `05-how.md`?
- ¿Cada item de fuera de alcance en `03-what.md` tiene razón documentada?
- ¿Las dependencias de terceros de `03-what.md` tienen entradas en `07-risks.md`?

Para cada link roto: anota el ID de origen, el ID destino que falta, y en qué archivo.

## Paso 4: Validación de Nivel 3 — Readiness

Evalúa si el PRD está listo para ingeniería:
- ¿Hay `[COHERENCE-FLAG]` sin resolver?
- ¿Hay REQs P0 sin criterios de aceptación?
- ¿Hay preguntas abiertas (`[UNRESOLVED]`, `[PM-DECISION]`) que bloquean decisiones de diseño técnico?
- ¿Hay USs con servicios externos sin escenarios de falla Gherkin?
- ¿Hay términos usados en REQs o Gherkin que no están definidos en `glossary.md`?

Si hay términos sin definir en el glosario, márcalos: `[GLOSARIO-PENDIENTE: término]`

## Paso 5: Escribir check-report.md

```markdown
---
command: /check
feature: [nombre]
template: [template]
readiness: ok | not-ready
critical-issues: [N]
last-updated: [YYYY-MM-DD]
---

# PRD Coherence Report — [Nombre del Feature]
**Template**: [Nombre] | **Generado**: [Fecha]

---

## READINESS: [✅ LISTO | ⚠️ NO LISTO — N issues críticos]

---

## Crítico — bloquea desarrollo

[C1] [Descripción específica: qué falta, dónde está, qué comando corre para resolverlo]

## Advertencia — resolver antes de compartir

[W1] [Descripción específica]

## Flags — baja prioridad

[F1] [Descripción]

---

## Completitud de secciones

| Sección | Archivo | Estado | Notas |
|---------|---------|--------|-------|
| Problema / JTBD | 01-why.md | ✓ Completo | |
| Evidencia | 01-why.md | ⚠ Incompleto | 1 [NEEDS-EVIDENCE] |
| Métricas de éxito | 04-success.md | ✓ Completo | |
| REQs P0 | 05-how.md | ⚠ Incompleto | REQ-007 sin criterios |
| Reglas de negocio | 05-how.md | ✓ Completo | |
| Historias de usuario | 06-stories.md | ✓ Completo | |
| Gherkin (happy paths) | 06-stories.md | ✓ Completo | |
| Gherkin (sad paths) | 06-stories.md | ⚠ Parcial | US-003 sin sad path |
| Riesgos | 07-risks.md | ✓ Completo | |
| Preguntas abiertas | 07-risks.md | ⚑ 2 abiertas | Q1, Q3 sin responsable |

---

## Trazabilidad

✓ / ⚠ [descripción de qué pasa con la trazabilidad]

---

## Glosario

✓ / ⚠ [N términos en REQs/Gherkin sin definición en glossary.md]
[Si hay: `[GLOSARIO-PENDIENTE: término1]`, `[GLOSARIO-PENDIENTE: término2]`]

---

## Próximos pasos

1. [Acción concreta con el comando específico a correr]
2. [Acción concreta]
```

## Paso 6: Commit

// turbo
```bash
git add prds/check-report.md
git commit -m "prd(check): reporte coherencia [feature-name] [readiness: ok|not-ready]

Issues críticos: [N]
Advertencias: [N]
Flags: [N]"
```

## Paso 7: Mostrar reporte y señal de completitud

Muestra el reporte completo en el chat (no solo el resumen). Luego:

```
✓ /check completado → check-report.md
Readiness: [✅ LISTO | ⚠️ NO LISTO]
Issues críticos: [N] | Advertencias: [N] | Flags: [N]
Git: commit [hash corto]
[Si listo]: PRD listo para /compile
[Si no listo]: Resuelve los issues críticos y corre /check de nuevo
```
