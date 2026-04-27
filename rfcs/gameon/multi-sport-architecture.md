# RFC — Multi-Sport Architecture

**Estado:** 🟡 Pendiente de decisión de Mario
**Detectado en:** Session 31 — April 27, 2026
**Autor:** Orion

---

## Por qué existe este RFC

GameOn empieza con fútbol pero la visión es multi-deporte. Antes de añadir un segundo deporte, necesitamos decidir cómo los datos específicos de cada deporte conviven en la misma entidad `Match` sin que la arquitectura se rompa.

La decisión tomada hoy afecta:
- Migraciones futuras
- El modelo de datos de `Match` y `MatchParticipant`
- El pitch component y el sistema de spots
- Los gameModes y cómo se filtran
- La experiencia del organizador al crear un partido

**Regla de este RFC:** no se toca hasta que estemos listos para implementar un segundo deporte real. Mientras tanto, seguimos puliendo fútbol.

---

## El problema de fondo

Cada deporte tiene su propio universo de conceptos que hoy están implícitamente acoplados a fútbol:

```
Fútbol:      gameMode = 5vs5..11vs11  |  positions = POR, DFC, MC...  |  field = pitch SVG
Pádel:       gameMode = 2vs2          |  positions = ninguna           |  field = court SVG
Baloncesto:  gameMode = 5vs5          |  positions = PG, SG, SF, PF, C |  field = court SVG
```

Hoy `Match.gameMode` es un string libre. Si mañana pádel tiene `"2vs2"` y fútbol tiene `"7vs7"`, conviven sin problema — pero el selector de gameMode en frontend mostraría todo mezclado sin saber qué opciones corresponden a qué deporte.

`MatchParticipant.position` es un número que mapea a spots del campo de fútbol. En pádel ese número no tiene semántica. No rompe nada, pero genera confusión.

---

## Lo que NO cambia con cualquier opción que elijamos

- Inscripción a partidos (`MatchParticipant`) — no toca sport
- Organizaciones y comunidades — relación por `organizationId`, agnóstica al deporte
- Torneos — `TournamentMatch` referencia `Match` por id
- Auth, roles, JWT
- Visibilidad, precio, límites de plan

---

## Opciones arquitectónicas

### Opción A — Columna `sport` + `gameMode` como string libre (mínimo)

Añadir solo `sport: enum Sport` a `Match`. El `gameMode` sigue siendo un string libre. El frontend filtra las opciones de gameMode según el deporte seleccionado usando un config estático.

```typescript
export enum Sport {
  FOOTBALL = 'football',
  PADEL    = 'padel',
}

// Match entity
sport: Sport  // default FOOTBALL
gameMode: string  // '5vs5', '7vs7', '2vs2' — sigue siendo libre
```

**Pros:** migration mínima, cero riesgo, fácil de revertir
**Contras:** sin constraint de DB entre sport y gameMode — un partido de pádel podría tener gameMode `11vs11` sin que nadie lo detecte
**Cuándo usarla:** si queremos probar el pipeline de deploy y avanzar rápido

---

### Opción B — Columna `sport` + `sportMetadata: jsonb`

Añadir `sport` y un campo JSONB que contiene los datos específicos del deporte. El backend valida el contenido según el sport con un validator por deporte.

```typescript
// Match entity
sport: Sport           // enum, default FOOTBALL
sportMetadata: jsonb   // nullable, validado por sport

// Ejemplo fútbol:
{ "gameMode": "7vs7" }

// Ejemplo pádel:
{ "gameMode": "2vs2", "courtType": "indoor" }
```

**Pros:** extensible sin migraciones por cada nuevo atributo, un solo punto de extensión
**Contras:** pierde type-safety en DB, requiere validación en capa de servicio, más complejo de debuggear
**Cuándo usarla:** cuando tengamos un segundo deporte real con atributos distintos confirmados

---

### Opción C — Tabla `SportConfig` separada (máxima escalabilidad)

Los gameModes y configuraciones de cada deporte viven en una tabla de catálogo, no en el código.

```sql
CREATE TABLE sport_configs (
  sport       VARCHAR(50) PRIMARY KEY,
  game_modes  jsonb,   -- ["5vs5", "6vs6", ...]
  has_spots   boolean,
  has_positions boolean
);
```

**Pros:** añadir un deporte = insertar una fila, sin deploy
**Contras:** complejidad innecesaria para la escala actual, over-engineering claro
**Cuándo usarla:** cuando GameOn tenga 10+ deportes activos con usuarios reales

---

## El tema de los spots y positions

`MatchParticipant.position` hoy es un número que mapea a un spot del campo de fútbol. Opciones:

**Opción 1 — Dejarlo como está:** en deportes sin spots, `position` es `null`. Convive sin romper nada.

**Opción 2 — Moverlo a `sportMetadata`:** el participant solo tiene `userId` + `matchId`. Los datos de posición van en el metadata. Más limpio pero requiere migrar los datos existentes.

**Recomendación de Orion:** Opción 1 mientras tanto. Opción 2 cuando tengamos el segundo deporte real y sepamos exactamente qué necesitamos.

---

## El pitch component

El issue #117 ya tiene el plan correcto: un registry `Sport → SportConfig` con el field component y los spots por gameMode. Ese plan es válido para cualquier opción arquitectónica que elijamos aquí. No está bloqueado por este RFC.

---

## Preguntas para Mario antes de decidir

1. **¿Cuál es el segundo deporte real?** ¿Pádel? ¿Baloncesto? La respuesta cambia el análisis — pádel es simple (2vs2, sin posiciones), baloncesto tiene más complejidad.

2. **¿El segundo deporte tiene atributos únicos** que fútbol no tiene? (tipo de pista, sets, cuartos...)

3. **¿Cuándo queremos tener el segundo deporte?** ¿Es un objetivo de los próximos 3 meses o es visión a largo plazo?

4. **¿El organizador puede crear partidos de cualquier deporte** desde el mismo panel, o cada deporte tiene su propia sección?

---

## Recomendación de Orion

**Hacer Opción A ahora** — solo añadir `sport` a `Match`. Es la migration más segura, nos sirve para probar el pipeline completo de deploy, y no cierra ninguna puerta.

**Cuando llegue el segundo deporte real** — retomar este RFC, responder las preguntas de negocio, y decidir si necesitamos Opción B.

La Opción C no la consideramos hasta tener tracción real con múltiples deportes.

---

## Estado

- [ ] Mario responde las preguntas de negocio
- [ ] Orion propone decisión final basada en respuestas
- [ ] Documentar en `DECISIONS.md`
- [ ] Crear issues de implementación

---

*Orion OS v1.5.0 — Session 31 — April 27, 2026*
