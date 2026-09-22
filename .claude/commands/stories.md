---
description: Escribe historias de usuario con Gherkin completo (happy paths, sad paths, edge cases, flujos E2E) y escenarios de falla de servicios externos obligatorios. Diseñado para facilitar la verificación automática. Escribe 06-stories.md.
---

Tu trabajo en esta sesión es convertir los requerimientos en comportamiento observable y verificable. El Gherkin que escribas será la especificación ejecutable que QA y el equipo de desarrollo usarán como definición de "hecho". Un `Then` vago es un Gherkin inútil — cada resultado debe ser verificable sin ambigüedad. Pero más allá de los escenarios individuales, tu trabajo es pensar sistémicamente: ¿qué pasa cuando los flujos se cruzan, cuando los estados se combinan, cuando los servicios fallan a mitad de camino?

## Paso 1: Leer el contexto

Lee `prds/00-context.md`, `02-who.md`, `03-what.md`, `04-success.md`, `05-how.md`.

Si no existe `05-how.md`:
> "⚠️ No encuentro `05-how.md`. Sin requerimientos definidos, las historias no tendrán trazabilidad. ¿Continuamos o corres `/how` primero?"

Extrae y ten presentes durante toda la sesión:
- Las **capacidades clave** de `03-what.md` — cada una genera una US.
- Las **dependencias de servicios externos** de `03-what.md` — cada servicio genera escenarios de falla obligatorios en cada US que lo toque.
- Los **REQs y sus criterios de aceptación** de `05-how.md` — son la base de los scenarios.
- Las **reglas de negocio y tablas de decisión** de `05-how.md` — cada condición de la tabla debe tener al menos un escenario.
- La **máquina de estados** (si existe en las RNs) — cada transición válida necesita un escenario; las transiciones inválidas también.
- Los **goals de éxito** de `04-success.md` — para la trazabilidad.

## Paso 1b: Verificar si la sección ya existe

Lee `prds/06-stories.md`. Si existe:

> "Ya existe `06-stories.md` para este feature. Esto es lo que contiene:
> - Historias de usuario: [N] (US-001 a US-[N])
> - Scenarios Gherkin: [N total aproximado]
> - Flujos E2E: [N]
> - Flags abiertos: [N] ([desglose por tipo])
>
> Voy a mejorar esta sección con tu input adicional en lugar de reescribirla desde cero."

Entra en **modo mejora**:
- Lee el contenido completo del archivo existente.
- Si el PM pasó argumentos al invocar el comando (`$ARGUMENTS`), úsalos como contexto para focalizar las mejoras. Pregunta qué quiere cambiar o agregar, referenciando el contenido actual.
- Si no hay argumentos, pregunta:
  > "¿Qué quieres mejorar o agregar a las historias de usuario? Puede ser: agregar edge cases, refinar scenarios existentes, agregar nuevos flujos E2E, cubrir nuevos REQs, o resolver flags abiertos."
- Haz **máximo 3 preguntas focalizadas** sobre lo que el PM quiere cambiar — no repitas la secuencia completa de derivación.
- Al escribir el archivo, preserva todo el contenido existente que no fue modificado. Actualiza `last-updated` y recalcula `flags` en el frontmatter.
- Continúa con los pasos de /clarify, glossary, y commit como de costumbre.
- En el commit, usa el verbo "actualizar":
  ```
  prd(stories): actualizar [descripción del cambio] [flags: N]
  ```

Si el archivo **no existe**, continúa con el Paso 2 (flujo de creación normal).

## Paso 2: Derivar historias y escenarios

### 2.1 — Una historia por capacidad clave

Por cada capacidad de `03-what.md`, estructura una US. Primero confirma la historia con el PM:
> "Para la capacidad '[capacidad X]', la historia sería: 'Como [segmento del dominio], quiero [capacidad] para [beneficio del JTBD].' ¿Es correcto?"

### 2.2 — Cuatro capas de escenarios por historia

Para cada US, genera escenarios en cuatro capas. **No preguntes al PM por cada capa** — deriva los escenarios sistemáticamente desde los REQs, RNs y tablas de decisión, y luego valida con el PM solo lo que no puedas inferir.

**Capa 1 — Happy paths**:
El usuario hace todo correctamente, el sistema funciona perfectamente. Incluye:
- El flujo principal descrito en el REQ.
- Si hay variantes por tipo de dato o perfil (Scenario Outline con Examples), inclúyelas.

**Capa 2 — Sad paths** (errores esperados del negocio):
Derivalos directamente de los criterios de aceptación del REQ y de las reglas de negocio:
- Validaciones que rechazan input (formato, tamaño, estado inválido).
- Transiciones de estado prohibidas por la máquina de estados.
- Reglas de negocio que producen resultado negativo.
- El usuario no tiene permiso o no está autorizado.

**Capa 3 — Edge cases** (condiciones límite y combinaciones inesperadas):
Esta es la capa que diferencia un PRD promedio de uno robusto. Genera escenarios para cada una de estas categorías que aplique:

| Categoría de edge case | Ejemplo | Cuándo aplicar |
|------------------------|---------|----------------|
| **Valores límite** | Archivo de exactamente 15 MB, campo vacío (0 chars), valor mínimo/máximo | Siempre que un REQ defina un rango o límite numérico |
| **Datos malformados** | Extensión falsificada (MIME ≠ extensión), caracteres especiales en nombres, unicode inesperado | Cuando el REQ acepta input del usuario |
| **Concurrencia** | Dos usuarios editan el mismo recurso, múltiples operaciones simultáneas sobre el mismo expediente | Cuando el recurso es compartido entre roles o sesiones |
| **Idempotencia** | La misma operación se ejecuta dos veces (retry, doble-clic, evento duplicado) | Cuando la operación tiene side effects (crear recurso, disparar notificación, cambiar estado) |
| **Interrupción y recuperación** | Red cae a mitad de operación, sesión expira, browser se cierra sin guardar | Cuando la operación tiene múltiples pasos o requiere tiempo significativo |
| **Estado intermedio / parcial** | Expediente parcialmente completo, extracción en progreso, algunos campos OK y otros no | Cuando el recurso tiene un ciclo de vida con estados |
| **Transición de estado regresiva** | Recurso que "vuelve atrás" (RADICADO → PENDIENTE_REENVÍO) | Cuando la máquina de estados permite revisión o corrección posterior |
| **Cambio de contexto** | El perfil del prospecto cambia después de cargar documentos, el requisito de documentos se actualiza | Cuando hay condiciones dinámicas que afectan la lista de requerimientos |
| **Operación sobre recurso en estado inesperado** | Intentar editar un doc ya RADICADO, intentar lanzar Scoring sin datos, intentar asignar tarea a usuario inexistente | Siempre — para cada estado final, intenta la operación que solo debería funcionar en un estado anterior |

No todos los edge cases aplican a todas las historias. Evalúa cuáles son relevantes y genera solo los que tengan impacto real. Si un edge case resulta irrelevante, no lo fuerces.

**Capa 4 — Falla de servicio externo** (si la US toca un servicio de `03-what.md`):

No preguntes si el PM quiere estos escenarios — son obligatorios. Genera automáticamente para cada servicio externo que toca la US:

1. **Servicio no disponible** (timeout, error de conexión): ¿qué ve el usuario? ¿en qué estado queda el recurso? ¿se puede reintentar?
2. **Respuesta inválida del proveedor** (payload vacío, estructura inesperada, error de negocio): ¿cómo lo interpreta el sistema? ¿qué acción toma?
3. **Servicio se recupera después de falla** (si aplica): ¿el sistema puede reintentar automáticamente? ¿el usuario puede solicitar reintento manual?

Solo pregunta al PM cómo debe comportarse el sistema si la respuesta no es inferible desde el contexto:
> "Esta historia usa [servicio externo]. Voy a agregar escenarios obligatorios de falla. ¿Qué ve exactamente el usuario cuando [servicio] no está disponible y qué hace el sistema con el estado de la operación?"

## Paso 3: Gherkin de calidad

Convierte las narrativas a Gherkin siguiendo estas reglas:

### Estructura de cada scenario
- **Given**: estado inicial del sistema y del usuario (pre-condición). Debe ser verificable y no ambiguo.
- **When**: **una sola acción** del usuario o del sistema. Si necesitas dos acciones, son dos scenarios.
- **Then**: resultado **observable y verificable**. Prohibido: "funciona correctamente", "responde adecuadamente", "se procesa exitosamente".
- **And**: resultados adicionales del mismo tipo (Given/When/Then).

### Regla crítica de verificabilidad

Cada `Then` debe responder al menos una de estas preguntas:
- ¿Qué ve el usuario en pantalla? (texto, estado, indicador visual)
- ¿Qué dato aparece, cambia o desaparece?
- ¿Qué mensaje específico recibe el usuario?
- ¿En qué estado queda el objeto del dominio después de la acción?
- ¿Qué registro se creó en el log de auditoría?
- ¿En cuánto tiempo ocurrió? (si hay SLA definido)

### Scenario Outline (Integral, Google PRD)
Cuando hay múltiples variantes del mismo flujo con datos diferentes, usa `Scenario Outline` con tabla `Examples` en lugar de repetir scenarios casi idénticos. Cada fila de la tabla debe representar una combinación significativamente distinta.

### Organización visual
Separa las capas de escenarios con comentarios en el bloque Gherkin:
```gherkin
# ── HAPPY PATH ─────────────
# ── SAD PATHS ──────────────
# ── EDGE CASES ─────────────
```

### Trazabilidad
Cada US debe referenciar al menos un REQ de `05-how.md`. Si no encuentra el REQ correspondiente, señálalo:
> "Esta historia cubre una capacidad que no aparece en los REQs de `/how`. ¿Agrego un REQ nuevo o es un duplicado de uno existente?"

## Paso 4: Generar flujos de integración de punta a punta (E2E)

Después de escribir las USs individuales, genera una sección **"Flujos de Integración"** con escenarios que cruzan múltiples historias. Estos son los tests de aceptación de negocio más valiosos — validan que el sistema funciona como un todo, no solo en partes aisladas.

### Flujos obligatorios (generar siempre):

1. **FLUJO happy path completo**: Un usuario recorre el camino perfecto de principio a fin. Cubre todas las USs en secuencia. El resultado final es verificable contra el JTBD de `01-why.md`.

2. **FLUJO con recuperación de error**: El usuario encuentra un error a mitad del camino (rechazo, falla de servicio, dato inválido), lo resuelve, y completa el proceso exitosamente. Demuestra que el sistema es resiliente.

3. **FLUJO con falla de servicio externo y corrección manual**: Un servicio de terceros falla, el sistema degrada elegantemente, el usuario interviene manualmente, y el proceso se completa. Cubre los escenarios de cuarentena / fallback.

### Flujos condicionales (generar cuando aplique):

4. **FLUJO con perfil complejo**: Si hay condiciones que modifican el flujo (tipos de usuario, sectores, condiciones combinadas), genera un escenario con el perfil más complejo posible que active todas las condiciones.

5. **FLUJO con interrupción y recuperación**: El usuario deja el proceso a medias, retoma en una sesión posterior, y completa exitosamente. Valida persistencia de estado intermedio.

6. **FLUJO con estados regresivos**: Un recurso avanza, luego retrocede por corrección o rechazo, y vuelve a avanzar. Valida que las transiciones regresivas no corrompen datos.

### Formato de los flujos E2E

Los flujos E2E se escriben como scenarios Gherkin extendidos con comentarios que marcan las etapas:

```gherkin
Scenario: [Descripción del flujo completo en una oración]
  Given [contexto inicial completo]

  # Etapa 1: [nombre de la etapa]
  When [acción]
  Then [resultado verificable]

  # Etapa 2: [nombre de la etapa]
  When [acción]
  Then [resultado verificable]

  # Etapa final: [validación de resultado de negocio]
  Then [resultado conectado al JTBD]
```

## Paso 5: Escribir 06-stories.md

```markdown
---
command: /stories
feature: [nombre]
template: [template]
status: complete
flags: [N]
last-updated: [YYYY-MM-DD]
clarify-status: ok
---

# Historias de Usuario — [Nombre del Feature]

---

**US-001** — [Nombre de la capacidad]
*Referencias: REQ-[N], RN-[N] | Goal: [G1]*

Como [rol del dominio Motai],
quiero [capacidad en lenguaje de negocio]
para [beneficio conectado al JTBD].

\```gherkin
Background:
  Given [estado del sistema relevante para todas las historias de este US]

# ── HAPPY PATH ──────────────────────────────────────────────────────────────

Scenario: [nombre descriptivo del camino feliz]
  Given [pre-condición específica]
  When [acción única del usuario]
  Then [resultado observable y verificable]
  And [resultado adicional]

Scenario Outline: [nombre — cuando hay variantes por datos]
  Given [pre-condición con "<variable>"]
  When [acción]
  Then [resultado con "<variable>"]

  Examples:
    | variable | ... |
    | valor_1  | ... |

# ── SAD PATHS ────────────────────────────────────────────────────────────────

Scenario: Error — [tipo de error derivado del REQ/RN]
  Given [pre-condición]
  When [acción que lleva al error]
  Then [mensaje específico que ve el usuario]
  And [estado del sistema después del error — nada queda inconsistente]

# ── EDGE CASES ───────────────────────────────────────────────────────────────

Scenario: Edge — [categoría: valor límite / concurrencia / idempotencia / etc.]
  Given [condición límite o combinación inesperada]
  When [acción]
  Then [resultado verificable que demuestra robustez]

# ── FALLA DE SERVICIO EXTERNO (OBLIGATORIO) ──────────────────────────────────

Scenario: Servicio [nombre] no disponible
  Given [el usuario ha completado los pasos previos]
  And el servicio [nombre] no está disponible
  When el usuario intenta [acción que requiere el servicio]
  Then el sistema detecta la falla en ≤ [N] segundos
  And el usuario ve el mensaje "[texto exacto]"
  And [estado de la operación — nada queda inconsistente]
  And [el intento queda registrado en el log de auditoría]

Scenario: Servicio [nombre] — respuesta inválida del proveedor
  Given [contexto]
  When el servicio devuelve [payload vacío / estructura inesperada / error de negocio]
  Then el sistema interpreta el error y [acción específica]
  And [estado del recurso]
  And [registro en auditoría]

Scenario: Servicio [nombre] — recuperación después de falla (si aplica)
  Given [recurso quedó en estado de falla por indisponibilidad previa del servicio]
  And el servicio [nombre] se ha recuperado
  When [el usuario solicita reintento / el sistema reintenta automáticamente]
  Then [resultado si el reintento es exitoso]
  And [resultado si el reintento vuelve a fallar]
\```

*(Repetir para cada US)*

---

## Flujos de Integración — Escenarios de Punta a Punta

---

**FLUJO-001** — [Descripción del flujo completo — happy path E2E]

\```gherkin
Scenario: [Una oración que describe el recorrido completo]
  Given [contexto inicial completo del flujo]

  # Etapa 1: [nombre]
  When [acción]
  Then [resultado verificable]

  # Etapa N: [nombre]
  When [acción]
  Then [resultado verificable]

  # Resultado de negocio
  Then [resultado conectado directamente al JTBD de 01-why.md]
\```

*(Repetir para cada flujo E2E)*

---

## Trazabilidad

| US | REQ | RN | Goal |
|----|-----|----|------|
| US-001 | REQ-001, REQ-002 | RN-001 | [Goal de 04-success.md] |
| FLUJO-001 | REQ-001…N | RN-001…N | [Goals cubiertos] |
```

## Paso 6: Ejecutar /clarify automático

Lee `06-stories.md` junto con `05-how.md` y `03-what.md`. Verifica rigurosamente:

1. **Cobertura de sad paths**: ¿Cada US derivada de un REQ P0 tiene al menos un sad path?
2. **Cobertura de tablas de decisión**: ¿Cada condición de cada tabla de decisión de `/how` tiene al menos un escenario? Si RN-001 tiene 4 condiciones, ¿hay escenarios para cada una?
3. **Cobertura de máquina de estados**: ¿Cada transición válida tiene un escenario? ¿Las transiciones inválidas más probables también?
4. **Cobertura REQ↔US**: ¿Hay USs sin REQ correspondiente? ¿Hay REQs P0 sin US?
5. **Escenarios de falla obligatorios**: ¿Cada US que toca un servicio externo tiene los 2–3 escenarios de falla obligatorios?
6. **Flujos E2E**: ¿Los flujos de integración cubren al menos happy path completo + recuperación de error + falla de servicio?
7. **Verificabilidad de Then**: ¿Algún `Then` es vago o no verificable automáticamente?

Máximo 2–3 preguntas focalizadas al PM. Nunca bloquees.

## Paso 7: Extraer términos para glossary.md

Extrae acciones, estados, eventos y entidades del dominio que aparecen en los Given/When/Then y califican según los criterios de `CLAUDE.md`. Agrega los nuevos.

## Paso 8: Commit

```bash
git add prds/06-stories.md prds/glossary.md
git commit -m "prd(stories): [US-001 US-002 etc] con Gherkin completo [flags: N]

Feature: [nombre completo]
Historias: [N] | Scenarios: [N] | Flujos E2E: [N]"
```

## Paso 9: Señal de completitud

```
✓ /stories completado → 06-stories.md
Historias: [N] | Scenarios Gherkin: [N] | Flujos E2E: [N] | Trazabilidad: [N] links
Flags: [N] | Glosario: [N] términos nuevos
Git: commit [hash corto]
Próximo recomendado: /risks
```
