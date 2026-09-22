---
description: Define métricas de éxito con baseline y target, guardrails, y non-goals (Google PRD). Escribe 04-success.md.
---

Tu trabajo en esta sesión es definir qué significa ganar. No "mejorar la experiencia" — métricas específicas, medibles, con baseline actual y target alcanzable. Un target sin baseline no es una métrica: es un deseo.

## Paso 1: Leer el contexto

Lee `prds/00-context.md`, `01-why.md`, `02-who.md`, `03-what.md` si existen.

Si no existe `03-what.md`:
> "⚠️ No encuentro `03-what.md`. Sin la solución definida, las métricas quedarán abstractas. ¿Continuamos o corres `/what` primero?"

Extrae: template, JTBD, segmento primario, capacidades clave, costo de inacción.

## Paso 1b: Verificar si la sección ya existe

Lee `prds/04-success.md`. Si existe:

> "Ya existe `04-success.md` para este feature. Esto es lo que contiene:
> - Métricas: [lista de métricas con tipo leading/lagging]
> - Guardrails: [presentes / no definidos]
> - Flags abiertos: [N] ([desglose por tipo])
>
> Voy a mejorar esta sección con tu input adicional en lugar de reescribirla desde cero."

Entra en **modo mejora**:
- Lee el contenido completo del archivo existente.
- Si el PM pasó argumentos al invocar el comando (`$ARGUMENTS`), úsalos como contexto para focalizar las mejoras. Pregunta qué quiere cambiar o agregar, referenciando el contenido actual.
- Si no hay argumentos, pregunta:
  > "¿Qué quieres mejorar o agregar a las métricas de éxito? Puede ser: ajustar targets, agregar baselines, definir guardrails, agregar nuevas métricas, o resolver flags abiertos."
- Haz **máximo 3 preguntas focalizadas** sobre lo que el PM quiere cambiar — no repitas la secuencia completa de P1–P5.
- Al escribir el archivo, preserva todo el contenido existente que no fue modificado. Actualiza `last-updated` y recalcula `flags` en el frontmatter.
- Continúa con los pasos de /clarify, glossary, y commit como de costumbre.
- En el commit, usa el verbo "actualizar":
  ```
  prd(success): actualizar [descripción del cambio] [flags: N]
  ```

Si el archivo **no existe**, continúa con el Paso 2 (flujo de creación normal).

## Paso 2: Preguntas de métricas

**P1 — Éxito desde perspectiva de negocio**:
> "Si este feature funciona perfectamente, ¿qué mueve la aguja para Motai? Hablo de ventas, alquileres, financiamientos, tiempo de ciclo en trámites o tesorería, retención de clientes, reducción de carga operativa en tienda o taller — algo que impacte directamente los objetivos del negocio."

Si el PM da una métrica vaga (*"mejorar la experiencia"*):
> "Eso suena a que lo que realmente queremos medir es [métrica específica inferida del contexto]. ¿Es correcto? Y si es así, ¿cuál es el valor actual (baseline) y cuál sería un target ambicioso pero alcanzable en 90 días?"

**P2 — Éxito desde perspectiva del usuario**:
> "¿Qué puede hacer [segmento primario] después de este feature que antes no podía? ¿Cómo sabrías que están obteniendo ese valor — qué comportamiento observable indicaría éxito?"

Las métricas de usuario (tiempo en tarea, tasa de completitud, adopción) son leading indicators. Las métricas de negocio son lagging. Necesitas ambas.

**P3 — Baseline**:
> "Para cada métrica que identificamos, ¿cuál es el valor actual? Si no tienes el dato exacto, ¿tienes forma de medirlo antes del lanzamiento?"

**Regla inviolable**: Un target sin baseline se marca `[NEEDS-EVIDENCE: baseline no disponible. Acción: medir antes del lanzamiento]`. Díselo directamente:
> "No tenemos baseline para [métrica]. La marco como [NEEDS-EVIDENCE] — sin baseline, el target es un deseo, no una métrica."

**P4 — Guardrails** (Integral, Google PRD):
> "¿Qué métricas existentes no deben empeorar como consecuencia de este feature? Por ejemplo: ¿podría afectar el tiempo de respuesta del sistema, la tasa de aprobación de créditos, o la carga de trabajo de operaciones?"

**P5 — Non-Goals** (solo Google PRD):
> "Para este template necesitamos ser explícitos sobre qué NO optimizaremos. Dime 3 cosas que alguien podría razonablemente esperar que este feature mejore, pero que conscientemente decidimos no priorizar. Cada non-goal necesita una razón."

**Profundidad por template**:
- **Lean**: P1 + P3 (1–2 métricas simples). P2 y P4 opcionales.
- **Integral**: P1–P4 (3 métricas SMART, leading + lagging).
- **PR/FAQ**: P1–P3 con enfoque en métricas desde perspectiva del cliente.
- **Google PRD**: P1–P5 completos (goals + non-goals + métricas + guardrails).

## Paso 3: Escribir 04-success.md

```markdown
---
command: /success
feature: [nombre]
template: [template]
status: complete
flags: [N]
last-updated: [YYYY-MM-DD]
clarify-status: ok
---

# Métricas de Éxito — [Nombre del Feature]

## Objetivos

| Métrica | Tipo | Baseline | Target | Plazo | Propietario | Fuente de datos |
|---------|------|---------|--------|-------|-------------|----------------|
| [Métrica] | Leading/Lagging | [Valor actual o NEEDS-EVIDENCE] | [Target] | [90d/180d] | [Nombre] | [Cómo se mide] |

## Guardrails
*(Omitir en template Lean)*

Métricas que no deben deteriorarse como consecuencia de este feature:
| Métrica protegida | Valor actual | Umbral mínimo aceptable |
|------------------|-------------|------------------------|
| [Métrica] | [Valor] | [Umbral] |

## Non-Goals
*(Solo Google PRD)*

Cosas que conscientemente NO optimizaremos:
| No optimizamos | Razón |
|----------------|-------|
| [Item] | [Razón] |
```

## Paso 4: Ejecutar /clarify automático

Lee `04-success.md` junto con los archivos anteriores. Verifica:
1. ¿Las métricas de éxito están conectadas al costo de inacción de `01-why.md`? Si el costo de inacción era "clientes que se van" pero las métricas no miden retención, señala la desconexión.
2. ¿Las métricas miden el éxito del segmento primario de `02-who.md`, no de los secundarios?
3. ¿Hay targets sin baseline que debería señalar?

Máximo 2–3 preguntas. Nunca bloquees.

## Paso 5: Extraer términos para glossary.md

Identifica métricas de negocio y procesos con nombres específicos del dominio. Agrega los que califican.

## Paso 6: Commit

// turbo
```bash
git add prds/04-success.md prds/glossary.md
git commit -m "prd(success): [métricas clave en 5 palabras] [flags: N]

Feature: [nombre completo]
Métricas: [N] | NEEDS-EVIDENCE: [N]"
git push
```

## Paso 7: Señal de completitud

```
✓ /success completado → 04-success.md
Métricas: [N] | NEEDS-EVIDENCE: [N] | Guardrails: [N]
Flags: [N] | Glosario: [N] términos nuevos
Git: commit [hash corto]
Próximo recomendado: /how
```
