# Contexto de dominio — Motai

Este archivo es la fuente de verdad que los comandos `/start` a `/compile` usan para anclar preguntas, sugerencias y el glosario al negocio real de Motai. Edítalo cuando el dominio cambie — nuevos roles, nuevos sistemas, nueva regulación.

## Qué es Motai

Motai cubre el 360° del negocio de motos: compra, venta, reparación (taller), financiamiento y renta de motocicletas. Opera con tiendas físicas en Bogotá y Medellín.

## Panorama de sistemas

| Sistema | Qué es | Alcance |
|---------|--------|---------|
| **Website** (motai.app) | Cara pública al cliente | Catálogo, cotización, captación |
| **Torque** | Backoffice operativo | Procesos core del negocio: comprar, vender, rentar, desistir, devolución, cancelar |
| **Backoffice administrativo** (nuevo, incluye `solicitud_compras`) | Backoffice interno, no operativo-comercial | Procesos administrativos: solicitudes de compra y otros procesos de soporte al negocio |

Cuando un feature depende de datos u operaciones de Website o Torque, trátalo como dependencia cross-sistema en `/what` y `/risks`, aunque ambos sean de Motai — son sistemas distintos con sus propios dueños.

## Roles del dominio

Usuarios típicos que interactúan con los procesos de Motai (lista abierta — agrega los que aparezcan en features nuevos):

- **Asesor de venta** — atiende clientes en tienda, origina ventas/rentas/financiamientos
- **Taller** — ejecuta reparaciones y mantenimiento
- **Trámites** — gestiona documentación y procesos administrativos/legales de las operaciones
- **Tesorería** — gestiona flujo de caja, pagos, desembolsos
- **Contabilidad** — registro contable y cierre financiero
- **Cartera** — gestión de cuentas por cobrar, financiamiento, mora
- **Facturación** — emisión y gestión de facturas

Para features del backoffice administrativo, considera también roles puramente administrativos (compras, aprobadores, solicitantes internos) aunque no estén en esta lista — no fuerces el mapeo a los roles comerciales de arriba si el feature es interno.

## Regulación aplicable

- **SARLAFT** — prevención de lavado de activos y financiación del terrorismo. Aplica sobre todo a operaciones de financiamiento y cartera.
- **Habeas Data** — protección de datos personales de clientes y empleados. Aplica a cualquier feature que capture, almacene o transmita datos personales.

No asumas otra regulación (ej. reportes ante la Superintendencia Financiera) salvo que el PM la mencione explícitamente para ese feature — no es de aplicación general confirmada todavía.

## Criterios para el glosario (`glossary.md`)

Cada comando extrae candidatos al lenguaje ubicuo al final de su ejecución. Un término califica si:

1. **Es específico del dominio Motai** — no es vocabulario genérico de software (ej. "usuario", "sistema", "formulario" no califican; "cartera", "desistimiento", "RADICADO" sí).
2. **Se usa de forma consistente** — el mismo término debe significar lo mismo en todo el PRD; si el PM usa dos palabras para lo mismo, resuélvelo antes de agregarlo.
3. **Es una entidad, estado, evento, rol o política de negocio** — no una descripción de UI o de implementación técnica.
4. **No es redundante** con un término ya presente en `glossary.md` (revisa antes de agregar).

Tipo DDD a asignar en la tabla del glosario: `Entidad`, `Value Object`, `Evento`, `Rol`, `Política/Regla`, `Estado`.

## Flags tipificados

Los comandos marcan huecos de información en el texto con estos tags. `/check` y `/compile` los consolidan:

| Flag | Uso |
|------|-----|
| `[ASSUMPTION: ...]` | Estimado o supuesto sin dato real de respaldo |
| `[NEEDS-EVIDENCE: ...]` | Falta evidencia real (dato, entrevista, métrica) — regla inviolable en `/why` y `/success` |
| `[UNRESOLVED: ...]` | Decisión pendiente que bloquea diseño o desarrollo |
| `[PM-DECISION: ...]` | Punto que requiere que el PM decida explícitamente entre opciones |
| `[COHERENCE-FLAG: ...]` | Detectado por `/clarify` o `/check` — contradicción entre secciones |
| `[GLOSARIO-PENDIENTE: término]` | Término usado en REQs/Gherkin sin definición en `glossary.md` |

## Convención de nombres de feature

`kebab-case`, descriptivo del feature, ej. `solicitud-compras-aprobacion-multinivel`.
