---
description: Traduce la solución en requerimientos funcionales verificables y reglas de negocio con tablas de decisión. Propaga criterios de falla para dependencias de terceros. Escribe 05-how.md.
---

Tu trabajo en esta sesión es el de un analista de negocio. Tomas las capacidades de `03-what.md` y las conviertes en bloques REQ-XXX con criterios de aceptación verificables, y en bloques RN-XXX con tablas de decisión exhaustivas. Todo requerimiento que QA no pueda verificar con un test concreto es un requerimiento incompleto.

## Paso 1: Leer el contexto

Lee `prds/00-context.md`, `01-why.md`, `02-who.md`, `03-what.md`, `04-success.md`.

Si no existe `04-success.md`:
> "⚠️ No encuentro `04-success.md`. Sin goals definidos, los requerimientos no podrán referenciar objetivos medibles — crítico en templates Integral y Google PRD. ¿Continuamos o corres `/success` primero?"

Si `03-what.md` tiene una sección `## Dependencias de Servicios Externos`, guarda esa información — la usarás para agregar criterios de falla a los REQs que tocan esos servicios.

## Paso 1b: Verificar si la sección ya existe

Lee `prds/05-how.md`. Si existe:

> "Ya existe `05-how.md` para este feature. Esto es lo que contiene:
> - REQs: [N] (P0: [N], P1: [N], P2: [N])
> - Reglas de negocio: [N]
> - NFRs: [N]
> - Flags abiertos: [N] ([desglose por tipo])
>
> Voy a mejorar esta sección con tu input adicional en lugar de reescribirla desde cero."

Entra en **modo mejora**:
- Lee el contenido completo del archivo existente.
- Si el PM pasó argumentos al invocar el comando (`$ARGUMENTS`), úsalos como contexto para focalizar las mejoras. Pregunta qué quiere cambiar o agregar, referenciando el contenido actual.
- Si no hay argumentos, pregunta:
  > "¿Qué quieres mejorar o agregar a los requerimientos? Puede ser: agregar nuevos REQs, refinar criterios de aceptación, completar reglas de negocio, agregar NFRs, o resolver flags abiertos."
- Haz **máximo 3 preguntas focalizadas** sobre lo que el PM quiere cambiar — no repitas la secuencia completa de derivación.
- Al escribir el archivo, preserva todo el contenido existente que no fue modificado. Actualiza `last-updated` y recalcula `flags` en el frontmatter.
- Continúa con los pasos de /clarify, glossary, y commit como de costumbre.
- En el commit, usa el verbo "actualizar":
  ```
  prd(how): actualizar [descripción del cambio] [flags: N]
  ```

Si el archivo **no existe**, continúa con el Paso 2 (flujo de creación normal).

## Paso 2: Derivar requerimientos desde las capacidades

Por cada capacidad de `03-what.md`:

> "Para la capacidad '[capacidad X]': ¿qué debe hacer el sistema exactamente? Piénsalo como: 'El sistema debe [verbo] [objeto] cuando [condición], de modo que [resultado].'"

Reformula las respuestas del PM en voz activa y en forma verificable. Si el PM dice *"que sea fácil subir documentos"*, convierte: *"El sistema debe permitir al [rol del usuario] cargar documentos en formato PDF, JPG o PNG de hasta 10MB mediante drag-and-drop o selector de archivos."*

Para cada requerimiento, pide criterios de aceptación:
> "¿Cómo sabe QA que este requerimiento está cumplido? Dame 2–3 criterios específicos y observables."

Si los criterios son vagos (*"que funcione bien"*):
> "Ese criterio no es verificable. Debe ser verdadero o falso sin ambigüedad. Por ejemplo: '[ ] El archivo aparece en la lista de documentos del cliente en ≤ 3 segundos.' ¿Puedes reformularlo así?"

Prioriza con MoSCoW: P0 (bloqueador), P1 (importante), P2 (deseable). Fuerza al PM a tener al menos 50% como P1/P2 — si todo es P0, pide que justifique cada uno.

## Paso 3: Detectar y modelar reglas de negocio

Lee los requerimientos buscando lógica condicional: *"si"*, *"cuando"*, *"excepto"*, *"a menos que"*, *"dependiendo del estado"*, *"según el tipo"*.

Al detectar una condición:
> "El requerimiento REQ-[N] tiene lógica condicional — el comportamiento cambia según [condición]. Vamos a convertirlo en una tabla de decisión. ¿Cuáles son todos los posibles valores de [condición A] y qué debe hacer el sistema en cada caso?"

Construye la tabla verificando exhaustividad:
> "¿Hay combinaciones de condiciones que no cubrimos? Si no especificamos qué pasa en [combinación X], el desarrollo asumirá algún comportamiento por defecto que puede no ser el correcto."

## Paso 4: Identificar NFRs relevantes

Para el contexto Motai, pregunta proactivamente:
- **Habeas Data**: ¿Este feature procesa, almacena o transmite datos personales de clientes o empleados? ¿Cuál es la base legal del tratamiento?
- **SARLAFT**: ¿Hay listas de control, scoring de riesgo o validaciones de cumplimiento involucradas (ej. en financiamiento o cartera)?
- **Rendimiento**: ¿Hay SLA de tiempo de respuesta para las operaciones principales?
- **Disponibilidad**: ¿Cuál es la disponibilidad requerida?

## Paso 5: Propagar criterios de terceros (si aplica)

Si `03-what.md` tiene dependencias de servicios externos, para cada REQ que toca esos servicios agrega automáticamente criterios de aceptación de falla:

```markdown
- **Criterios de Aceptación (falla del servicio [nombre])**:
  - [ ] El sistema detecta timeout o error del servicio en ≤ [N] segundos
  - [ ] El usuario recibe un mensaje de error específico y accionable
  - [ ] La operación no queda en estado inconsistente
  - [ ] El sistema registra el intento para reintento o auditoría
```

Y agrega en la tabla de NFRs:
```markdown
| Integración con [servicio] | Disponibilidad ≥ [SLA del proveedor] | Health check activo | Plan de contingencia documentado |
```

## Paso 6: Escribir 05-how.md

```markdown
---
command: /how
feature: [nombre]
template: [template]
status: complete
flags: [N]
last-updated: [YYYY-MM-DD]
clarify-status: ok
---

# Requerimientos y Reglas de Negocio — [Nombre del Feature]

## Requerimientos Funcionales

**REQ-001** · P0 — [Nombre descriptivo]
El sistema debe [verbo activo] [objeto] cuando [condición], de modo que [resultado].
Referencia: [G1 / objetivo de 04-success.md] *(solo Integral y Google PRD)*

- **Criterios de Aceptación**:
  - [ ] [Criterio específico y verificable]
  - [ ] [Criterio específico y verificable]

*(Repetir para cada REQ)*

---

## Reglas de Negocio

**RN-001** — [Nombre de la regla]
[Descripción en lenguaje de negocio]

| [Condición A] | [Condición B] | Resultado del sistema |
|--------------|--------------|----------------------|
| [Valor 1] | [Valor X] | [Qué hace el sistema] |
| [Valor 2] | [Valor X] | [Qué hace el sistema] |

*(Repetir para cada RN)*

---

## Requerimientos No Funcionales

| Categoría | Requerimiento | Medición | Contingencia |
|-----------|--------------|---------|-------------|
| Rendimiento | [SLA] | [Métrica] | [Plan] |
| Disponibilidad | [%] | [Método] | [Plan] |
| Habeas Data | [Base legal] | [Revisión legal] | — |
| [Integración externa] | [SLA del proveedor] | Health check | [Plan de contingencia] |
```

## Paso 7: Ejecutar /clarify automático

Lee `05-how.md` junto con todos los archivos anteriores. Verifica:
1. ¿Hay contradicciones entre REQs (ej: REQ-001 dice automático pero REQ-004 dice manual)?
2. ¿Las tablas de decisión son exhaustivas? ¿Hay combinaciones no cubiertas?
3. ¿Cada REQ P0 sirve a al menos un goal de `04-success.md`?
4. ¿Alguna capacidad de `03-what.md` quedó sin requerimiento correspondiente?

Máximo 2–3 preguntas. Nunca bloquees.

## Paso 8: Extraer términos para glossary.md

Identifica entidades, estados, eventos y políticas con nombres específicos del dominio. Agrega los que califican.

## Paso 9: Commit

// turbo
```bash
git add prds/05-how.md prds/glossary.md
git commit -m "prd(how): [descripción de los REQs principales] [flags: N]

Feature: [nombre completo]
REQs: [N] (P0: [N], P1: [N], P2: [N])
Reglas de negocio: [N]"
```

## Paso 10: Señal de completitud

```
✓ /how completado → 05-how.md
REQs: [N] (P0: [N], P1: [N], P2: [N]) | RNs: [N] | NFRs: [N]
Flags: [N] | Glosario: [N] términos nuevos
Git: commit [hash corto]
Próximo recomendado: /stories
```
