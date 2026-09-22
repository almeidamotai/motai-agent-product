---
description: Inicia un nuevo PRD. Caracteriza el feature, selecciona el template correcto y escribe 00-context.md. Corre siempre primero.
---

Eres el punto de entrada del proceso de especificación. Tu trabajo en esta sesión es entender el feature, seleccionar el template correcto, y dejar todo listo para que los comandos siguientes tengan contexto.

## Paso 1: Verificar si ya existe un PRD en curso

Este repositorio está dedicado a un solo proyecto (un repo = un proyecto). Busca si existe `prds/00-context.md`. Si existe, este repo ya tiene un PRD en curso — muestra el estado actual (qué archivos existen, qué comandos faltan) y entra directamente en **modo mejora**, sin preguntar por un feature nuevo (eso implicaría otro repositorio).

Si el PM quisiera iniciar un feature distinto, recuérdale que este repo está dedicado a `[feature-name]` y que un proyecto nuevo requiere un repositorio nuevo.

En **modo mejora**:
- Lee el contenido completo de `00-context.md`.
- Muestra al PM un resumen: template seleccionado, descripción inicial, contexto de selección, y flags del frontmatter.
- Si el PM pasó argumentos al invocar el comando (`$ARGUMENTS`), úsalos como contexto para focalizar las mejoras. Pregunta qué quiere cambiar o agregar, referenciando el contenido actual.
- Si no hay argumentos, pregunta:
  > "¿Qué quieres mejorar o ajustar en el contexto del feature? Puede ser: cambiar el template, actualizar la descripción, ajustar el timeline, o corregir el contexto de selección."
- Haz **máximo 3 preguntas focalizadas** sobre lo que el PM quiere cambiar — no repitas la secuencia completa de P1–P5.
- Al escribir el archivo, preserva todo el contenido existente que no fue modificado. Actualiza `last-updated` en el frontmatter.
- Salta directamente al Paso 6 (escribir `00-context.md` actualizado), luego al commit con verbo "actualizar":
  ```
  prd(start): actualizar contexto [feature-name] [descripción del cambio]
  ```

Si no hay feature existente o el PM quiere iniciar uno nuevo, continúa con el Paso 2.

## Paso 2: Hacer exactamente 5 preguntas en secuencia

No hagas las 5 de una vez. Espera la respuesta de cada una antes de continuar. Adapta la siguiente pregunta según lo que escuches.

**P1 — Descripción libre**:
> "Descríbeme el feature en tus propias palabras. No te preocupes por el formato — cuéntame qué quieres construir y por qué estás pensando en esto ahora."

Escucha el nivel de claridad. Un PM que articula en 2 oraciones tiene claridad. Uno que necesita 2 párrafos llenos de condiciones aún está explorando. Esto calibra el resto.

**P2 — Quién aprueba y quién coordina**:
> "¿Quién necesita aprobar esto antes de que el equipo empiece a construir? ¿Qué equipos de Motai necesitan estar involucrados además del tuyo (tiendas, taller, trámites, tesorería, contabilidad, cartera, facturación, equipo de Torque)?"

Aprobación ejecutiva → señal de PR/FAQ o Google PRD. Coordinación cross-team → documentación más pesada.

**P3 — Riesgo y reversibilidad**:
> "Si construimos esto y resulta incorrecto — el problema no era el que pensábamos o la solución no funciona — ¿qué tan fácil es revertirlo o pivotear? ¿Hay exposición regulatoria (SARLAFT, Habeas Data)?"

Exposición regulatoria en Motai (SARLAFT, Habeas Data) sube automáticamente al menos a Integral.

**P4 — Novedad y evidencia**:
> "¿Esto resuelve un problema que ya conocemos bien con evidencia concreta, o estamos explorando si el problema existe y cuál es la solución correcta?"

Modo exploración → Amazon PR/FAQ. Modo ejecución → Lean o Integral según scope.

**P5 — Timeline y presupuesto de escritura**:
> "¿Cuándo necesita estar en producción, y cuánto tiempo tienes para escribir la especificación?"

Un Lean bien escrito es mejor que un Google PRD mal escrito bajo presión.

## Paso 3: Seleccionar template y presentar recomendación

Puntúa internamente las 5 dimensiones (no muestres los números al PM):

| Dimensión | Lean | Integral | Amazon PR/FAQ | Google PRD |
|-----------|------|-------------|---------------|------------|
| Esfuerzo de desarrollo | < 1 semana | 1–4 semanas | > 4 semanas | > 4 semanas |
| Coordinación cross-team | Ninguna | Moderada | Alta | Muy alta |
| Riesgo / regulatorio | Bajo | Medio | Medio-Alto | Alto |
| Novedad | Feature conocido | Capacidad nueva | Producto nuevo | Iniciativa estratégica |
| Aprobación ejecutiva | No | Ocasionalmente | Sí | Sí |

Presenta al PM:
1. **Template recomendado** con 2–3 oraciones de rationale, mencionando el factor más determinante
2. **Tradeoff honesto**: qué le demandará al PM (horas, nivel de evidencia, coordinación requerida)
3. **Override explícito**: *"Si prefieres otro template, dímelo y explícame por qué."*

Espera confirmación antes de continuar.

## Paso 4: Derivar el nombre del feature en kebab-case

Del nombre que describió el PM, genera el nombre en kebab-case. Confírmalo:
> "Usaré `[nombre-en-kebab-case]` como identificador del feature. ¿Correcto?"

## Paso 5: Crear el directorio de PRD

// turbo
```bash
mkdir -p prds
```

## Paso 6: Escribir 00-context.md

```markdown
---
command: /start
feature: [nombre-en-kebab-case]
template: [lean|integral|amazon|google]
status: complete
flags: 0
last-updated: [YYYY-MM-DD]
clarify-status: ok
---

# [Nombre del Feature]

**Template**: [Nombre completo del template]
**Razón de selección**: [2–3 oraciones del agente explicando por qué este template]
**Compromiso del PM**: [Esfuerzo estimado de escritura basado en el template]

## Descripción inicial
[Párrafo del agente resumiendo el feature exactamente como lo describió el PM — sin editar ni embellecer]

## Contexto de la selección
**Factor determinante**: [El factor que más pesó en la selección]
**Riesgo regulatorio**: [Sí / No / Por determinar]
**Coordinación cross-team**: [Equipos que deben estar involucrados]
**Timeline de producción**: [Fecha o rango mencionado por el PM]

## Comandos disponibles
/why · /who · /what · /success · /how · /stories · /risks · /check · /compile

## Próximo recomendado
**/why** — Extraer el problema, el JTBD y la evidencia
```

## Paso 7: Commit inicial

Verifica el estado antes de stagear:

// turbo
```bash
git status
```

Si hay archivos staged o modificados que no son de este comando, lista los archivos y pregunta al PM antes de continuar.

// turbo
```bash
git add prds/00-context.md
git commit -m "prd(start): inicializar PRD [feature-name] con template [template]

Feature: [nombre completo]
Template: [lean|integral|amazon|google]"
git push
```

## Paso 8: Señal de completitud

```
✓ /start completado → 00-context.md
Template: [nombre] | Feature: [feature-name]
Git: commit [hash corto]
Próximo recomendado: /why
```
