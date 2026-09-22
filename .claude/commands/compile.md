---
description: Ensambla todos los archivos de sección en prd.md siguiendo la estructura del template. Genera resumen ejecutivo, historial del proceso, y consolidado de flags. Corre al final, después de /check.
---

Eres el paso final del proceso. Tu trabajo es leer todos los archivos de sección que el PM construyó a través de múltiples sesiones y ensamblarlos en un único `prd.md` coherente. No generas contenido nuevo — ensamblas lo que el PM ya aprobó. La única excepción es el Resumen Ejecutivo, que sintetizas de todos los archivos.

## Paso 1: Leer el template y el inventario de secciones

Lee `prds/00-context.md` para determinar el template.

Verifica qué archivos existen. Si faltan archivos requeridos por el template:

```
⚠️ Faltan secciones requeridas por el template [nombre]:
  - [archivo.md] → no se ha corrido /[comando]
  - [archivo.md] → no se ha corrido /[comando]

¿Cómo prefieres proceder?
a) Continuar — las secciones faltantes aparecerán como [INCOMPLETE] en prd.md
b) Cancelar — correr los comandos faltantes primero
```

Espera respuesta del PM antes de continuar.

## Paso 2: Generar el Resumen Ejecutivo

Lee todos los archivos de sección. Sintetiza un Resumen Ejecutivo de 3–5 párrafos que capture:
- El problema y su evidencia (de `01-why.md`)
- A quién afecta y quién es el segmento primario (de `02-who.md`)
- Qué se construye y qué queda fuera (de `03-what.md`)
- Cómo se medirá el éxito (de `04-success.md`)
- Los riesgos principales y el plan de lanzamiento (de `07-risks.md`)

Muéstraselo al PM:
> "Generé el siguiente Resumen Ejecutivo. ¿Lo apruebas o quieres ajustarlo?"

Espera aprobación. Ajusta si el PM pide cambios. El Resumen Ejecutivo es el único contenido que generas creativamente.

## Paso 3: Mapear secciones al template

Mapeo para template **Integral** (ajustar según el template activo):

| Sección en prd.md | Fuente |
|-------------------|--------|
| Metadata + Resumen Ejecutivo | `00-context.md` + síntesis aprobada |
| Problema, JTBD, Evidencia, Costo de inacción | `01-why.md` |
| Segmentos de usuario | `02-who.md` |
| Solución, capacidades, escenarios, fuera de alcance | `03-what.md` |
| Objetivos, métricas, guardrails | `04-success.md` |
| REQs, RNs, NFRs | `05-how.md` |
| Historias de usuario, Gherkin | `06-stories.md` |
| Riesgos, dependencias, preguntas abiertas, go/no-go | `07-risks.md` |
| Glosario | `glossary.md` |
| Historial del proceso | Generado desde frontmatter de todos los archivos |
| Flags abiertos consolidados | Extraído de todos los archivos |

El orden en `prd.md` sigue la estructura del template — no el orden en que el PM corrió los comandos.

## Paso 4: Verificar referencias cruzadas

Antes de escribir el archivo, verifica que las referencias cruzadas sean consistentes:
- REQs mencionados en `06-stories.md` existen en `05-how.md`
- RNs referenciados en Gherkin tienen tabla de decisión en `05-how.md`
- Goals (G1, G2, etc.) mencionados en REQs están definidos en `04-success.md`

Si encuentra referencias rotas, márcalas en el `prd.md` con `[COHERENCE-FLAG: referencia a REQ-XXX no encontrada en 05-how.md]`. No bloquees la generación.

## Paso 5: Escribir prd.md

Ensambla el documento completo usando la estructura de abajo. Para cada sección, copia el contenido del archivo fuente **sin el frontmatter YAML** (las líneas entre `---`). Respeta los headings originales de cada archivo pero ajusta su nivel para que encajen en la jerarquía del documento final (los `#` del archivo fuente se convierten en `##` o `###` según corresponda).

### Estructura del documento (template Integral)

Escribe `prd.md` siguiendo exactamente esta estructura. Reemplaza los bloques `{contenido de XX-archivo.md}` con el contenido real de cada archivo (sin frontmatter).

```markdown
---
feature: [nombre del feature]
template: [template]
compiled: [fecha YYYY-MM-DD]
sections: [N]/[N] completas
open-flags: [N total]
---

# PRD: [Título del feature desde 00-context.md]

## Resumen Ejecutivo

[Resumen ejecutivo aprobado por el PM en Paso 2 — 3 a 5 párrafos]

---

## 1. Problema

{contenido de 01-why.md: Pain Point, JTBD, Usuarios afectados, Evidencia, Costo de inacción}

---

## 2. Usuarios

{contenido de 02-who.md: segmentos de usuario, perfiles, diferenciación}

---

## 3. Solución

{contenido de 03-what.md: capacidades clave, escenarios, alternativas descartadas, fuera de alcance, dependencias de servicios externos}

---

## 4. Métricas de Éxito

{contenido de 04-success.md: objetivos, métricas con baseline/target, guardrails, non-goals}

---

## 5. Requerimientos y Reglas de Negocio

{contenido de 05-how.md: REQs funcionales, reglas de negocio con tablas de decisión, NFRs}

---

## 6. Historias de Usuario

{contenido de 06-stories.md: user stories con Gherkin completo, tabla de trazabilidad}

---

## 7. Riesgos y Dependencias

{contenido de 07-risks.md: riesgos, dependencias, preguntas abiertas, criterios go/no-go}

---

## 8. Glosario

{contenido de glossary.md: tabla de términos del lenguaje ubicuo}

---

## 9. Historial del Proceso de Especificación

| Comando | Archivo | Fecha | Flags al cierre |
|---------|---------|-------|----------------|
| /start  | 00-context.md | [fecha del frontmatter] | [flags del frontmatter] |
| /why    | 01-why.md | [fecha] | [N] ([desglose por tipo]) |
| /who    | 02-who.md | [fecha] | [N] |
| /what   | 03-what.md | [fecha] | [N] |
| /success | 04-success.md | [fecha] | [N] |
| /how    | 05-how.md | [fecha] | [N] |
| /stories | 06-stories.md | [fecha] | [N] |
| /risks  | 07-risks.md | [fecha] | [N] |
| /compile | prd.md | [fecha de hoy] | — |

**Flags abiertos en el PRD final**: [N total]
**Issues críticos**: [N] — [lista si > 0]

---

## 10. Flags Abiertos

### [NEEDS-EVIDENCE] ([N])
- [descripción exacta del flag] → [sección donde aparece]

### [ASSUMPTION] ([N])
- [descripción exacta del flag] → [sección donde aparece]

### [UNRESOLVED] ([N])
- [descripción exacta del flag] → [sección donde aparece]

### [COHERENCE-FLAG] ([N])
- [descripción] → [sección donde aparece]
```

### Reglas de ensamblaje

1. **Sin frontmatter en el cuerpo**: Al copiar contenido de cada archivo fuente, elimina las líneas entre los delimitadores `---` del YAML frontmatter. El único frontmatter es el del propio `prd.md`.
2. **Headings ajustados**: El `#` principal de cada archivo fuente se convierte en el `##` de la sección correspondiente en `prd.md`. Los sub-headings bajan un nivel (`##` → `###`, `###` → `####`).
3. **Secciones faltantes**: Si un archivo no existe, escribe la sección con el heading correspondiente seguido de `[INCOMPLETE] — esta sección no ha sido generada. Correr /[comando] para completarla.`
4. **Flags consolidados**: Extrae todos los flags tipificados (`[NEEDS-EVIDENCE: ...]`, `[ASSUMPTION: ...]`, `[UNRESOLVED: ...]`, `[COHERENCE-FLAG: ...]`) de todo el documento ensamblado y agrúpalos en la sección 10. Si no hay flags de un tipo, omite ese sub-heading.
5. **Referencias cruzadas rotas** detectadas en Paso 4 se insertan inline donde corresponda como `[COHERENCE-FLAG: ...]` y también aparecen en la sección 10.

## Paso 6: Commit

// turbo
```bash
git add prds/prd.md
git commit -m "prd(compile): PRD final [template] [feature-name] [flags: N]

Secciones: [N]/[N] completas
Issues críticos: [N]
Flags: NEEDS-EVIDENCE:[N] ASSUMPTION:[N] UNRESOLVED:[N]"
```

## Paso 7: Señal de completitud

```
✓ /compile completado → prd.md

Template: [nombre]
Secciones ensambladas: [N]/[N]
Flags abiertos: [NEEDS-EVIDENCE: N] [ASSUMPTION: N] [UNRESOLVED: N]

Git: commit [hash corto]

PRD listo para compartir con ingeniería.
Archivo: prds/prd.md
```
