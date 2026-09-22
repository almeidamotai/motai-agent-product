---
description: Detecta y resuelve inconsistencias después de cada comando. Corre automáticamente al final de /why, /who, /what, /success, /how, /stories, /risks. No requiere invocación explícita del PM.
---

Este comando corre automáticamente. No esperes que el PM lo invoque. Tu trabajo es encontrar inconsistencias introducidas por el comando que acaba de ejecutarse antes de que se propaguen al resto del documento.

## Paso 1: Identificar el archivo recién escrito

Determina cuál es el archivo que acaba de ser creado o modificado en esta sesión (el de mayor número de prefijo en `prds/`).

## Paso 2: Leer el scope completo

Lee el archivo recién escrito **y todos los archivos de sección anteriores** (por orden numérico de prefijo).

## Paso 3: Ejecutar tres verificaciones en orden de prioridad

**Verificación 1 — Coherencia interna del archivo recién escrito**

¿La sección es coherente consigo misma? Busca:
- Afirmaciones contradictorias dentro del mismo archivo
- Un REQ que dice "automático" y otro que dice "manual" para el mismo flujo
- Una tabla de decisión donde dos filas tienen las mismas condiciones pero resultados diferentes
- Un segmento marcado como primario con volumen menor al secundario sin justificación

**Verificación 2 — Coherencia con secciones anteriores**

¿Lo que se acaba de escribir contradice algo ya establecido?

Ejemplos a buscar:
- `02-who.md` identifica a [rol primario] como único usuario primario, pero un REQ en `05-how.md` menciona que [otro rol] puede completar el flujo sin intervención del usuario primario
- El JTBD de `01-why.md` describe un problema de tiempo, pero las métricas de `04-success.md` no miden tiempo
- Una capacidad en `03-what.md` no tiene ningún REQ correspondiente en `05-how.md`

**Verificación 3 — Gaps de especificación**

¿Hay algo que debería estar especificado pero no lo está?

Ejemplos:
- `05-how.md` menciona "estado Análisis de Crédito" pero ese estado no aparece definido en ningún otro archivo
- `06-stories.md` tiene una US que toca un servicio externo declarado en `03-what.md` pero no tiene escenario de falla

## Paso 4: Actuar sobre lo que encuentres

**Si no hay inconsistencias**:
```
─── /clarify ────────────────────────────────
Revisando coherencia después de /[comando]...
✓ Sin inconsistencias detectadas
clarify-status: ok
─────────────────────────────────────────────
```
Actualiza `clarify-status: ok` en el frontmatter del archivo recién escrito.

**Si hay inconsistencias**:
- Prioriza por gravedad (la que más bloquearía el desarrollo primero)
- Presenta **máximo 2–3 preguntas** — nunca más. Si hay más issues, marca los de menor prioridad como `[COHERENCE-FLAG]` en el archivo correspondiente y sigue
- Distingue inconsistencia de decisión de diseño: pregunta antes de asumir que es un error

```
─── /clarify ────────────────────────────────────────────────
Revisando coherencia después de /[comando]...

⚠ Inconsistencia detectada:

[Descripción concreta de qué se contradice y dónde]

¿Cuál es la intención correcta?
a) [Opción A]
b) [Opción B]
c) [Opción C — si aplica]

[PM responde]

✓ Decisión registrada. [Archivo/s] actualizado/s.
─────────────────────────────────────────────────────────────
```

Actualiza los archivos afectados. Actualiza `clarify-status` en el frontmatter.

**Si el PM dice "es intencional, continúa"**: registra la decisión como comentario en el frontmatter o en el cuerpo del archivo y avanza sin bloquear.

## Paso 5: Git — amend, no nuevo commit

Las correcciones de `/clarify` son parte de la misma unidad de trabajo del comando que las generó:

// turbo
```bash
git add prds/[archivo-modificado]
git commit --amend --no-edit
```

**Excepción**: si `/clarify` modifica un archivo distinto al que acaba de escribir el comando que lo disparó (es decir, corrige una sección de un comando anterior), crea un commit nuevo en lugar de hacer amend:

// turbo
```bash
git add prds/[archivo-de-comando-anterior]
git commit -m "prd(clarify): resolver inconsistencia en [archivo] detectada en /[comando]"
```
