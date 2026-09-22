---
description: Consolida riesgos, dependencias, preguntas abiertas y criterios go/no-go. Pre-popula riesgos de integración desde 03-what.md. Escribe 07-risks.md.
---

Tu trabajo en esta sesión es identificar lo que puede salir mal y definir los criterios objetivos que determinarán si el feature está listo para lanzarse. Los criterios go/no-go deben ser verificables por QA, Legal y Ops sin necesidad de tu juicio subjetivo.

## Paso 1: Leer el contexto

Lee todos los archivos existentes: `00-context.md` a `06-stories.md` y `glossary.md`.

Mientras lees, extrae todos los flags `[UNRESOLVED: ...]` y `[PM-DECISION: ...]` que encuentres en cualquier archivo. Los consolidarás en la sección de preguntas abiertas.

Si `03-what.md` tiene una sección `## Dependencias de Servicios Externos`, extrae cada servicio — los usarás para pre-generar entradas de riesgo de integración.

Si no existe `05-how.md`:
> "⚠️ No encuentro `05-how.md`. Sin los requerimientos definidos, la evaluación de riesgos será incompleta. ¿Continuamos o corres `/how` primero?"

## Paso 1b: Verificar si la sección ya existe

Lee `prds/07-risks.md`. Si existe:

> "Ya existe `07-risks.md` para este feature. Esto es lo que contiene:
> - Riesgos: [N]
> - Dependencias: [N]
> - Preguntas abiertas: [N]
> - Checklist go/no-go: [presente / no definido]
> - Flags abiertos: [N]
>
> Voy a mejorar esta sección con tu input adicional en lugar de reescribirla desde cero."

Entra en **modo mejora**:
- Lee el contenido completo del archivo existente.
- Si el PM pasó argumentos al invocar el comando (`$ARGUMENTS`), úsalos como contexto para focalizar las mejoras. Pregunta qué quiere cambiar o agregar, referenciando el contenido actual.
- Si no hay argumentos, pregunta:
  > "¿Qué quieres mejorar o agregar a los riesgos y plan de lanzamiento? Puede ser: agregar riesgos, actualizar dependencias, resolver preguntas abiertas, refinar criterios go/no-go, o ajustar el plan de lanzamiento."
- Haz **máximo 3 preguntas focalizadas** sobre lo que el PM quiere cambiar — no repitas la secuencia completa de P1–P5.
- Al escribir el archivo, preserva todo el contenido existente que no fue modificado. Actualiza `last-updated` y recalcula `flags` en el frontmatter.
- Continúa con los pasos de /clarify, glossary, y commit como de costumbre.
- En el commit, usa el verbo "actualizar":
  ```
  prd(risks): actualizar [descripción del cambio] [flags: N]
  ```

Si el archivo **no existe**, continúa con el Paso 2 (flujo de creación normal).

## Paso 2: Preguntas de riesgos y lanzamiento

**P1 — Los tres riesgos más grandes**:
> "Si este proyecto falla, ¿cuál es la causa más probable? Dame los tres escenarios de falla que más te preocupan."

Categoriza internamente cada riesgo: técnico · regulatorio · de adopción · de dependencia · de timing.

En contexto Motai, pregunta proactivamente:
> "¿Hay riesgo de incumplimiento SARLAFT? ¿Este feature requiere actualización de políticas de Habeas Data? ¿Depende de un sistema legado (website, Torque) con historial de inestabilidad?"

**P2 — Dependencias externas** (equipo e internas):
> "¿Qué necesitas de otros equipos o bounded contexts de Motai (tiendas, taller, trámites, tesorería, contabilidad, cartera, facturación, equipo de Torque)? Para cada uno: ¿quién es el dueño, cuándo lo necesitas, y qué pasa si no llega a tiempo?"

**Nota**: Si `03-what.md` tiene dependencias de servicios externos, NO hagas esta pregunta para esos servicios — ya tienes la información. Pre-genera sus entradas de riesgo directamente:

```
| Indisponibilidad de [servicio] | Integración | Media | Alto | Circuit breaker + fallback definido en REQ-[N] | [PM definirá propietario] |
| Cambio de API sin aviso del proveedor | Integración | Baja | Alto | Contrato con versionado; monitoreo de deprecations | [PM definirá propietario] |
| Latencia del servicio por encima del SLA | Integración | Alta | Medio | Timeout configurado en REQ-[N] | [PM definirá propietario] |
```

Luego pregunta solo lo que falta: propietarios y fechas.

**P3 — Preguntas abiertas**:
> "¿Qué decisiones importantes no hemos tomado todavía porque falta información o alineación?"

Antes de que responda, presenta los flags que extrajiste:
> "Encontré [N] preguntas abiertas marcadas en el documento. Las incluyo aquí — ¿puedes asignar responsables y fechas límite?"

**P4 — Plan de lanzamiento**:
> "¿Cómo quieres lanzar esto? ¿Rollout gradual o big bang? ¿Hay un segmento piloto natural — un subconjunto de usuarios con quienes probar primero?"

**P5 — Criterios go/no-go**:
> "¿Qué condiciones deben cumplirse para que digas 'estamos listos para lanzar'? Deben ser verificables por QA, Legal y Ops independientemente de tu juicio."

## Paso 3: Escribir 07-risks.md

```markdown
---
command: /risks
feature: [nombre]
template: [template]
status: complete
flags: [N]
last-updated: [YYYY-MM-DD]
clarify-status: ok
---

# Riesgos y Plan de Lanzamiento — [Nombre del Feature]

## Tabla de riesgos

| Riesgo | Categoría | Probabilidad | Impacto | Mitigación | Propietario |
|--------|-----------|-------------|---------|-----------|-------------|
| [Riesgo] | [Categoría] | Alta/Media/Baja | Alto/Medio/Bajo | [Acción concreta] | [Nombre] |

## Dependencias

| Dependencia | Equipo/Proveedor | Tipo | Fecha necesaria | Estado | Fallback |
|------------|-----------------|------|----------------|--------|---------|
| [Dependencia] | [Dueño] | Interna/Externa | [Fecha] | [Estado] | [Plan B] |

## Preguntas abiertas

| ID | Pregunta | Responsable | Deadline | Opciones | Impacto si no se resuelve |
|----|---------|------------|---------|---------|--------------------------|
| Q1 | [Pregunta] | [Nombre] | [Fecha] | [Opciones] | [Impacto] |

## Plan de lanzamiento

**Estrategia**: [Rollout gradual / Big bang]

| Fase | Segmento piloto | Criterio de entrada | Criterio de salida |
|------|----------------|--------------------|--------------------|
| Alpha | [Segmento] | [Criterio] | [Criterio] |
| Beta | [Segmento] | [Criterio] | [Criterio] |
| GA | Todos | [Criterio] | — |

## Checklist go/no-go

### Técnico
- [ ] [Criterio verificable por QA]

### Negocio
- [ ] [Criterio verificable por PM/Ops]

### Regulatorio
- [ ] [Criterio verificable por Legal]
```

## Paso 4: Ejecutar /clarify automático

Lee `07-risks.md` junto con todos los archivos anteriores. Verifica:
1. ¿Hay riesgos de integraciones de terceros declaradas en `03-what.md` que no aparecen en la tabla de riesgos?
2. ¿Los criterios go/no-go cubren los REQs P0 de `05-how.md`?
3. ¿Las preguntas abiertas consolidadas son las mismas que los flags `[UNRESOLVED]` del documento?

Máximo 2–3 preguntas. Nunca bloquees.

## Paso 5: Extraer términos para glossary.md

Identifica procesos de lanzamiento y términos del dominio que aparezcan. Agrega los que califican.

## Paso 6: Commit

// turbo
```bash
git add prds/07-risks.md prds/glossary.md
git commit -m "prd(risks): riesgos dependencias go-no-go [flags: N]

Feature: [nombre completo]
Riesgos: [N] | Dependencias: [N] | Preguntas abiertas: [N]"
git push
```

## Paso 7: Señal de completitud

```
✓ /risks completado → 07-risks.md
Riesgos: [N] | Dependencias: [N] | Preguntas abiertas: [N]
Flags: [N] | Glosario: [N] términos nuevos
Git: commit [hash corto]
PRD listo para /check y luego /compile
```
