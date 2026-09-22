---
description: Extrae el problema, el JTBD, la evidencia y el costo de inacción. Escribe 01-why.md. Corre después de /start.
---

Tu trabajo en esta sesión es externalizar completamente el modelo mental del PM sobre el problema: por qué existe, a quién afecta, cuánto cuesta, qué evidencia lo respalda. Al final debes poder escribir un 01-why.md que convenza a cualquier stakeholder de que el problema es real y vale la pena resolver.

## Paso 1: Leer el contexto

Lee `prds/00-context.md` si existe.

Si no existe:
> "⚠️ No encuentro `00-context.md`. Sin contexto base no tengo el template ni el feature identificado. ¿Corremos `/start` primero?"

Extrae: nombre del feature, template seleccionado, descripción inicial.

## Paso 1b: Verificar si la sección ya existe

Lee `prds/01-why.md`. Si existe:

> "Ya existe `01-why.md` para este feature. Esto es lo que contiene:
> - Pain point: [primera oración del pain point]
> - JTBD: [primera oración del JTBD]
> - Flags abiertos: [N] ([desglose por tipo])
>
> Voy a mejorar esta sección con tu input adicional en lugar de reescribirla desde cero."

Entra en **modo mejora**:
- Lee el contenido completo del archivo existente.
- Si el PM pasó argumentos al invocar el comando (`$ARGUMENTS`), úsalos como contexto para focalizar las mejoras. Pregunta qué quiere cambiar o agregar, referenciando el contenido actual.
- Si no hay argumentos, pregunta:
  > "¿Qué quieres mejorar o agregar a esta sección? Puede ser: refinar el pain point, agregar evidencia, resolver flags abiertos, actualizar el JTBD, o ajustar el costo de inacción."
- Haz **máximo 3 preguntas focalizadas** sobre lo que el PM quiere cambiar — no repitas la secuencia completa de P1–P5.
- Al escribir el archivo, preserva todo el contenido existente que no fue modificado. Actualiza `last-updated` y recalcula `flags` en el frontmatter.
- Continúa con los pasos de /clarify, glossary, y commit como de costumbre.
- En el commit, usa el verbo "actualizar":
  ```
  prd(why): actualizar [descripción del cambio] [flags: N]
  ```

Si el archivo **no existe**, continúa con el Paso 2 (flujo de creación normal).

## Paso 2: Hacer hasta 5 preguntas en secuencia

Haz una pregunta a la vez. Profundiza con follow-ups cuando la respuesta sea vaga antes de avanzar a la siguiente.

**P1 — El problema en voz del usuario**:
> "Descríbeme el problema desde la perspectiva del usuario que lo sufre. No me digas la solución — cuéntame qué le pasa a [rol relevante] cuando intenta [acción relevante] hoy."

Si la respuesta es vaga (*"la experiencia no es buena"*):
> "Necesito algo más concreto para el PRD. Dame un ejemplo específico: un [rol del usuario afectado] en una situación específica, haciendo algo específico, y qué sale mal exactamente."

**P2 — Cuantificación del dolor**:
> "¿Cuánto tiempo pierde alguien con este problema en una semana típica? ¿Con qué frecuencia ocurre? ¿Cuántas personas dentro de Motai (tiendas, taller, trámites, tesorería, contabilidad, cartera, facturación) o entre los clientes lo enfrentan?"

Si no tiene números:
> "Dame tu mejor estimado — lo marcamos como [ASSUMPTION] para validar después. ¿Qué necesitarías para tener el dato real?"

**P3 — Costo de inacción**:
> "Si Motai no resuelve esto en los próximos 6 meses, ¿qué pasa concretamente? ¿Ventas, alquileres o financiamientos que se pierden, clientes que se van a la competencia, personal operativo que renuncia por la carga de trabajo, riesgo regulatorio?"

Si no puede articularlo, el problema probablemente no es urgente. Nótalo pero no lo bloquees.

**P4 — Evidencia**:
> "¿Qué evidencia concreta tienes de que este problema existe y tiene el impacto que describes? Puede ser: entrevistas con usuarios, tickets de soporte, datos de analytics, quejas de clientes, reportes de operaciones."

**Regla inviolable**: Si el PM no tiene evidencia real, escribe `[NEEDS-EVIDENCE: descripción de qué se necesita y cómo obtenerlo]`. Díselo directamente:
> "No tengo evidencia real para incluir aquí. La marco como [NEEDS-EVIDENCE] — un PRD con datos fabricados lleva a decisiones incorrectas."

**P5 — JTBD**:
> "Completa esta frase: 'Cuando [situación específica], [rol] quiere [motivación] para [resultado esperado], pero actualmente [obstáculo concreto].' Empieza con la situación — ¿cuándo exactamente ocurre este problema?"

Una vez que el PM complete el JTBD, parafraséalo de vuelta:
> "Entonces lo que describes es: [paráfrasis]. ¿Lo capturé correctamente?"

Esto detecta malentendidos antes de que se propaguen al documento.

**Profundidad por template**:
- **Lean**: P1 + P5 son suficientes. P2–P4 opcionales si el PM los tiene.
- **Integral / PR/FAQ / Google PRD**: las 5 preguntas son requeridas.

## Paso 3: Escribir 01-why.md

```markdown
---
command: /why
feature: [nombre]
template: [template]
status: complete
flags: [N]
last-updated: [YYYY-MM-DD]
clarify-status: ok
---

# Problema — [Nombre del Feature]

## Pain Point
[Narrativa en voz del usuario. Específica, con el rol correcto del dominio Motai, describiendo la situación concreta y qué falla.]

## Jobs-to-be-Done
Cuando [situación específica], [rol] quiere [motivación] para [resultado esperado], pero actualmente [obstáculo concreto].

## Usuarios afectados
| Rol | Descripción del impacto | Estimado de volumen | Frecuencia |
|-----|------------------------|-------------------|------------|
| [Rol del dominio] | [Cómo les afecta] | [N] [ASSUMPTION si no hay dato real] | [Frecuencia] |

## Evidencia
[Fuentes reales con atribución. Si no hay, [NEEDS-EVIDENCE: qué se necesita y cómo obtenerlo].]

## Costo de inacción
**Para Motai**: [Impacto en ventas, alquileres, financiamientos, retención de clientes, eficiencia operativa, riesgo regulatorio]
**Para el usuario**: [Impacto en el trabajo diario del rol afectado]
[Marcar estimados como [ASSUMPTION: fuente del estimado]]
```

## Paso 4: Ejecutar /clarify automático

Lee `01-why.md` junto con `00-context.md`. Verifica:
1. ¿Hay contradicciones internas en `01-why.md`?
2. ¿Algo contradice lo que ya estaba en `00-context.md`?
3. ¿Falta algo que debería estar especificado?

Si detectas inconsistencias, presenta máximo 2–3 preguntas focalizadas. Si el PM dice "es intencional", registra la decisión y continúa. Nunca bloquees.

Si hubo correcciones, actualiza `clarify-status: flagged → ok` en el frontmatter.

## Paso 5: Extraer términos para glossary.md

Lee `01-why.md`. Identifica candidatos al lenguaje ubicuo siguiendo los criterios en `CLAUDE.md`. Lee `glossary.md` si existe y descarta duplicados. Agrega los términos nuevos que califican.

Estructura de glossary.md si no existe aún:
```markdown
---
feature: [nombre]
template: [template]
last-updated: [YYYY-MM-DD]
total-terms: [N]
---

# Glosario — [Nombre del Feature]

> Lenguaje ubicuo extraído durante el proceso de especificación.

| Término | Definición | Bounded Context | Tipo DDD | Extraído en |
|---------|-----------|----------------|----------|------------|
```

## Paso 6: Commit

// turbo
```bash
git add prds/01-why.md prds/glossary.md
git commit -m "prd(why): [descripción breve del problema] [flags: N]

Feature: [nombre completo]
Template: [template]
Flags abiertos: [tipos y cantidades]"
```

## Paso 7: Señal de completitud

```
✓ /why completado → 01-why.md
Problema: [1 oración del pain point]
JTBD: [1 oración del job]
Flags: [N] | Glosario: [N] términos nuevos
Git: commit [hash corto]
Próximo recomendado: /who
```
