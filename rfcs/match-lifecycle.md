# RFC — Match Lifecycle (ciclo de vida de un partido)

**Estado:** 🟡 Pendiente de decisión de Mario
**Detectado en:** Session 20 — April 9, 2026
**Autor:** Orion

---

## Problema

Todos los partidos quedan en `status: ACTIVE` para siempre. No existe ninguna lógica que los mueva a `COMPLETED` o `CANCELLED`. El enum ya existe en la entidad pero nunca se usa.

Esto afecta a:
- Las stats del organizador (partidos activos siempre = todos)
- La identidad del jugador (¿en qué partidos "participé" vs "me apunté"?)
- La vista pública (partidos del pasado siguen apareciendo como activos)
- El futuro sistema de reviews (¿cuándo puede el jugador valorar al organizador?)

---

## Contexto técnico (Orion)

El enum actual:
```typescript
export enum MatchStatus {
  ACTIVE    = 'active',
  COMPLETED = 'completed',
  CANCELLED = 'cancelled',
}
```

El campo `cancellationReason` **no existe** en la entidad — habría que añadirlo con una migración.

Opciones técnicas para cambiar el estado:
- **Manual** — el organizador lo cambia desde su panel
- **Automático** — un job/cron que detecta partidos cuya `dateTime` ya pasó y los marca como `COMPLETED`
- **Mixto** — automático para `COMPLETED`, manual para `CANCELLED`

---

## Opciones para Mario

### Opción A — Solo manual
El organizador decide cuándo completar o cancelar un partido desde su panel.

**Pros:** control total, simple de implementar
**Contras:** si el organizador no lo hace, el partido queda activo para siempre

### Opción B — Automático para COMPLETED, manual para CANCELLED
Un job que corra cada hora detecta partidos cuya `dateTime + duración` ya pasó y los marca `COMPLETED` automáticamente. El organizador solo gestiona cancelaciones.

**Pros:** no depende de que el organizador recuerde hacerlo
**Contras:** requiere un job/cron (más infraestructura). En Vercel serverless hay que usar un cron externo o Vercel Cron Jobs (disponible en planes de pago)

### Opción C — Mixto con confirmación
El sistema avisa al organizador "este partido terminó hace X horas, ¿fue exitoso?" y el organizador confirma o corrige.

**Pros:** combina automatización con control humano
**Contras:** más complejo de UX, requiere notificaciones

---

## Preguntas para Mario

Antes de decidir, necesito que respondas estas preguntas de negocio:

1. **¿Quién puede cancelar un partido?** ¿Solo el organizador? ¿También el creador si es un partido libre?

2. **¿El jugador recibe notificación cuando se cancela?** (email, WhatsApp)

3. **¿Un partido cancelado libera las plazas?** ¿O simplemente queda registrado como cancelado?

4. **¿El motivo de cancelación es visible para los jugadores** o solo para el organizador?

5. **¿Un partido completado cuenta como participación** en las stats del jugador?

6. **¿Puede el organizador reabrir un partido** que ya fue cancelado o completado?

7. **Para la demo con Jose** — ¿necesitamos esto antes de la demo o es post-demo?

---

## Decisión de Mario

> *(Edita esta sección cuando tengas claro el plan)*

**Opción elegida:**

**Respuestas a las preguntas:**
1.
2.
3.
4.
5.
6.
7.

**Notas adicionales:**

---

## Siguiente paso (Orion)

Cuando Mario complete la sección de decisión:
1. Documentar en `DECISIONS.md` como Dxx
2. Crear issues para Nestor (backend: migración + lógica) y Olga (frontend: botones en panel organizador)
