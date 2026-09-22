---
description: Define qué se construye y qué no. Capacidades clave, alternativas descartadas, fuera de alcance explícito. Detecta dependencias de terceros. Escribe 03-what.md.
---

Tu trabajo en esta sesión es definir la solución a nivel de capacidades (no de implementación) y trazar el alcance explícito que previene scope creep. La pregunta más importante de esta sesión no es "qué vamos a construir" — es "qué explícitamente no vamos a construir y por qué".

## Paso 1: Leer el contexto

Lee `prds/00-context.md`, `01-why.md`, `02-who.md` si existen.

Si no existe `01-why.md`:
> "⚠️ No encuentro `01-why.md`. Sin el problema definido, la solución puede no conectar con el dolor real. ¿Continuamos o corres `/why` primero?"

Extrae: template, JTBD del `/why`, segmento primario del `/who`.

## Paso 1b: Verificar si la sección ya existe

Lee `prds/03-what.md`. Si existe:

> "Ya existe `03-what.md` para este feature. Esto es lo que contiene:
> - Capacidades clave: [lista numerada de capacidades]
> - Fuera de alcance: [N] items
> - Dependencias externas: [lista o 'ninguna']
> - Flags abiertos: [N]
>
> Voy a mejorar esta sección con tu input adicional en lugar de reescribirla desde cero."

Entra en **modo mejora**:
- Lee el contenido completo del archivo existente.
- Si el PM pasó argumentos al invocar el comando (`$ARGUMENTS`), úsalos como contexto para focalizar las mejoras. Pregunta qué quiere cambiar o agregar, referenciando el contenido actual.
- Si no hay argumentos, pregunta:
  > "¿Qué quieres mejorar o agregar a la solución y alcance? Puede ser: agregar o quitar capacidades, actualizar fuera de alcance, agregar dependencias externas, refinar escenarios de usuario, o resolver flags abiertos."
- Haz **máximo 3 preguntas focalizadas** sobre lo que el PM quiere cambiar — no repitas la secuencia completa de P1–P5.
- Al escribir el archivo, preserva todo el contenido existente que no fue modificado. Actualiza `last-updated` y recalcula `flags` en el frontmatter.
- Continúa con los pasos de /clarify, glossary, y commit como de costumbre.
- En el commit, usa el verbo "actualizar":
  ```
  prd(what): actualizar [descripción del cambio] [flags: N]
  ```

Si el archivo **no existe**, continúa con el Paso 2 (flujo de creación normal).

## Paso 2: Preguntas de solución y alcance

**P1 — La solución en una oración**:
> "Sin decirme cómo se implementa técnicamente, describe qué podrá hacer el usuario que hoy no puede hacer. Una oración."

Valida que la capacidad descrita resuelva el JTBD de `01-why.md`. Si no lo resuelve:
> "La capacidad que describes resuelve [X], pero el JTBD dice que el usuario quiere [Y]. ¿Hay una desconexión o estoy malinterpretando?"

**P2 — Capacidades clave**:
> "Lista las 3–5 cosas más importantes que el sistema debe poder hacer para que esta solución funcione. Piénsalas como capacidades del sistema, no como pantallas o botones."

Si el PM lista más de 5:
> "Tengo [N] capacidades. Para mantener el scope controlado, ¿cuáles son las 3 sin las cuales la solución no tiene valor? Las demás pueden ser v2."

**P3 — Alternativas descartadas**:
> "¿Qué otras soluciones consideraste y por qué las descartaste? Documentarlo previene que el equipo replantee las mismas discusiones durante el desarrollo."

**Detección de dependencias de terceros** (durante P1–P3):

Escucha activamente si el PM menciona integraciones, APIs externas, proveedores, servicios de terceros, o datos que vienen de fuera del ecosistema Motai (website, Torque, el nuevo backoffice administrativo). Al detectarlo, para y pregunta:
> "Mencionaste [servicio externo]. Antes de seguir:
> 1. ¿Hay contrato o acuerdo vigente con este proveedor, o está por definir?
> 2. ¿Cuál es su SLA — disponibilidad comprometida y tiempo de respuesta?
> 3. ¿Qué hace el sistema si ese servicio no está disponible cuando el usuario lo necesita?"

Si hay dependencia de tercero, agrega automáticamente a `03-what.md` una sección `## Dependencias de Servicios Externos` y en fuera de alcance:
> | Comportamiento del servicio [nombre] cuando su infraestructura falla | Fuera de nuestro control — definimos cómo respondemos, no cómo falla el tercero | Nunca |

**P4 — Fuera de alcance** (la pregunta más importante):
> "Dime 3 cosas que alguien podría razonablemente esperar que este feature incluya, pero que explícitamente NO vas a construir en esta versión. Para cada una, explícame brevemente por qué."

Si el PM no puede identificar nada:
> "Generalmente hay funcionalidad que parece obvia incluir pero que agregaría complejidad no justificada. Basándome en el feature que describes, sugiero considerar: [2–3 sugerencias específicas del dominio Motai]. ¿Alguna de estas debería quedar explícitamente fuera de alcance?"

**P5 — Escenarios de usuario** (Integral, PR/FAQ, Google PRD; omitir en Lean):
> "Cuéntame un día en la vida de [segmento primario] después de que este feature existe. ¿Qué estaba haciendo antes de necesitarlo, qué lo llevó a usarlo, qué pasos siguió, y qué obtuvo al final?"

Convierte la narrativa libre en formato: Setup → Trigger → Acción → Resolución.

## Paso 3: Escribir 03-what.md

```markdown
---
command: /what
feature: [nombre]
template: [template]
status: complete
flags: [N]
last-updated: [YYYY-MM-DD]
clarify-status: ok
---

# Solución y Alcance — [Nombre del Feature]

## Descripción general
[Qué se construye en una oración, conectado directamente al JTBD de 01-why.md]

## Alternativas descartadas
| Alternativa | Razón del descarte |
|-------------|-------------------|
| [Alternativa] | [Razón concreta] |

## Capacidades clave
1. [Capacidad 1 — verbo + objeto, sin implementación técnica]
2. [Capacidad 2]
...
*(máx 5)*

## Escenarios de usuario
*(Omitir en template Lean)*

### Escenario: [Nombre]
**Setup**: [Contexto previo del usuario]
**Trigger**: [Qué lo llevó al feature]
**Acción**: [Pasos que sigue]
**Resolución**: [Qué obtiene al final]

## Fuera de alcance
| Qué no se construye | Razón | ¿Podría ser v2? |
|--------------------|-------|----------------|
| [Item] | [Razón] | [Sí / No / Fecha tentativa] |

## Dependencias de Servicios Externos
*(Omitir si no hay dependencias de terceros)*

| Servicio | Proveedor | Contrato vigente | SLA comprometido | Comportamiento si no disponible |
|----------|-----------|-----------------|-----------------|-------------------------------|
| [Nombre] | [Proveedor] | [Sí/No/Por definir] | [Disponibilidad · latencia] | [Falla elegante / reintento / alternativa] |
```

## Paso 4: Ejecutar /clarify automático

Lee `03-what.md` junto con `01-why.md` and `02-who.md`. Verifica:
1. ¿Las capacidades descritas resuelven el JTBD de `01-why.md`? Si no, señala la desconexión.
2. ¿Las capacidades sirven al segmento primario de `02-who.md`? ¿O sirven a un segmento diferente?
3. ¿Hay algo que el PM describió en `/why` como el problema principal pero que no aparece en las capacidades?

Máximo 2–3 preguntas. Nunca bloquees.

## Paso 5: Extraer términos para glossary.md

Identifica entidades del dominio, procesos y sistemas externos mencionados. Agrega los que califican.

## Paso 6: Commit

// turbo
```bash
git add prds/03-what.md prds/glossary.md
git commit -m "prd(what): [solución en 5 palabras] [flags: N]

Feature: [nombre completo]
Capacidades: [N] | Fuera de alcance: [N]
Dependencias externas: [Sí/No]"
```

## Paso 7: Señal de completitud

```
✓ /what completado → 03-what.md
Capacidades: [N] | Fuera de alcance: [N] | Dependencias externas: [N]
Flags: [N] | Glosario: [N] términos nuevos
Git: commit [hash corto]
Próximo recomendado: /success
```
