---
description: Define y caracteriza los segmentos de usuario afectados. Fuerza diferenciación entre segmentos. Escribe 02-who.md.
---

Tu trabajo en esta sesión es identificar todos los segmentos de usuario afectados por el feature, diferenciar cómo cada uno experimenta el problema, y forzar al PM a elegir un segmento primario. El error más común aquí es colapsar todos los usuarios en uno genérico — trabaja activamente contra eso.

## Paso 1: Leer el contexto

Lee `prds/00-context.md` y `prds/01-why.md` si existen.

Si no existe `01-why.md`:
> "⚠️ No encuentro `01-why.md`. Sin el problema definido, los segmentos no estarán anclados a un dolor real. ¿Continuamos o corres `/why` primero?"

Extrae del contexto: template, roles mencionados en el JTBD, usuarios afectados preliminares.

## Paso 1b: Verificar si la sección ya existe

Lee `prds/02-who.md`. Si existe:

> "Ya existe `02-who.md` para este feature. Esto es lo que contiene:
> - Segmentos: [lista de roles con su prioridad]
> - Segmento primario: [nombre del segmento primario]
> - Flags abiertos: [N]
>
> Voy a mejorar esta sección con tu input adicional en lugar de reescribirla desde cero."

Entra en **modo mejora**:
- Lee el contenido completo del archivo existente.
- Si el PM pasó argumentos al invocar el comando (`$ARGUMENTS`), úsalos como contexto para focalizar las mejoras. Pregunta qué quiere cambiar o agregar, referenciando el contenido actual.
- Si no hay argumentos, pregunta:
  > "¿Qué quieres mejorar o agregar a los segmentos de usuario? Puede ser: agregar un segmento, refinar la diferenciación, cambiar el segmento primario, actualizar volúmenes, o resolver flags abiertos."
- Haz **máximo 3 preguntas focalizadas** sobre lo que el PM quiere cambiar — no repitas la secuencia completa de P1–P4.
- Al escribir el archivo, preserva todo el contenido existente que no fue modificado. Actualiza `last-updated` y recalcula `flags` en el frontmatter.
- Continúa con los pasos de /clarify, glossary, y commit como de costumbre.
- En el commit, usa el verbo "actualizar":
  ```
  prd(who): actualizar [descripción del cambio] [flags: N]
  ```

Si el archivo **no existe**, continúa con el Paso 2 (flujo de creación normal).

## Paso 2: Preguntas de segmentación

**P1 — Todos los tipos de usuarios**:
> "¿Quiénes son todos los tipos de personas que interactuarán con este feature o cuyos flujos de trabajo cambian por él? No solo el usuario principal — también los que reciben el output, los que aprueban, los que resuelven errores."

Sugiere proactivamente los roles del dominio Motai relevantes según el contexto del feature (asesor de venta, taller, trámites, tesorería, contabilidad, cartera, facturación):
> "Considera todos los tipos de usuario del dominio: ¿quién origina la operación, quién la aprueba, quién la ejecuta, quién recibe el resultado?"

**P2 — Diferenciación por segmento**:

Para cada segmento identificado, pregunta:
> "¿Cómo experimenta [segmento X] el problema específicamente? ¿Qué hace hoy para trabajar alrededor de él? ¿Qué necesita de la solución que los otros segmentos no necesitan?"

Si las respuestas son casi idénticas para dos segmentos, señálalo:
> "Las necesidades de [segmento A] y [segmento B] parecen casi iguales. ¿Son realmente segmentos distintos o los colapsamos en uno?"

**P3 — Segmento primario**:
> "Si tuvieras que elegir un solo segmento para optimizar esta primera versión, ¿cuál sería y por qué?"

Esta decisión alimenta `/what` (scope) y `/success` (métricas). Si el PM no puede elegir:
> "No priorizar es también una decisión — significa que el feature intentará servir a todos y puede que no optimice para ninguno. ¿Para quién sería el fracaso más costoso si este feature no funciona bien?"

**P4 — Volumen y frecuencia**:
> "¿Cuántas personas hay en cada segmento? ¿Con qué frecuencia interactúan con el área del sistema que este feature afecta?"

Si no hay datos, `[ASSUMPTION]` con el estimado del PM. Nunca inventes volúmenes.

**Profundidad por template**:
- **Lean**: P1 + P3 obligatorias. P2 solo para el segmento primario.
- **Integral**: las 4 preguntas, 2–3 segmentos con contexto.
- **PR/FAQ / Google PRD**: las 4 preguntas, 3+ segmentos con pain diferenciado por cada uno.

## Paso 3: Escribir 02-who.md

```markdown
---
command: /who
feature: [nombre]
template: [template]
status: complete
flags: [N]
last-updated: [YYYY-MM-DD]
clarify-status: ok
---

# Segmentos de Usuario — [Nombre del Feature]

## Tabla de segmentos

| Rol | Descripción | Volumen estimado | Frecuencia | Prioridad |
|-----|------------|-----------------|------------|-----------|
| [Rol del dominio] | [Descripción] | [N] [ASSUMPTION si aplica] | [Frecuencia] | Primario / Secundario |

## Descripción por segmento

### [Segmento 1] — Primario
**Cómo experimenta el problema**: [Específico]
**Workaround actual**: [Qué hace hoy para trabajar alrededor del problema]
**Necesidad diferenciada**: [Qué necesita de la solución que otros segmentos no necesitan]

### [Segmento N] — Secundario
[Misma estructura]

## Segmento primario declarado
**[Nombre del segmento]** — [Justificación de 1–2 oraciones]

## Impacto en segmentos secundarios
[Qué esperan los segmentos secundarios del feature y qué no les optimizaremos en v1]
```

## Paso 4: Ejecutar /clarify automático

Lee `02-who.md` junto con `00-context.md` and `01-why.md`. Verifica:
1. ¿Los segmentos identificados aparecían implícitamente en el JTBD de `/why`? Si el JTBD mencionaba un rol que no está en la tabla, señálalo.
2. ¿Hay contradicciones internas (segmento marcado como primario con volumen menor al secundario sin justificación)?
3. ¿El segmento primario elegido es coherente con el pain point principal de `/why`?

Máximo 2–3 preguntas. Nunca bloquees.

## Paso 5: Extraer términos para glossary.md

Identifica roles y entidades del dominio que aparecen en `02-who.md` y califican para el glosario. Agrega los nuevos.

## Paso 6: Commit

// turbo
```bash
git add prds/02-who.md prds/glossary.md
git commit -m "prd(who): [segmentos identificados] [flags: N]

Feature: [nombre completo]
Segmentos: [lista breve]
Primario: [segmento elegido]"
```

## Paso 7: Señal de completitud

```
✓ /who completado → 02-who.md
Segmentos: [N] identificados | Primario: [nombre]
Flags: [N] | Glosario: [N] términos nuevos
Git: commit [hash corto]
Próximo recomendado: /what
```
