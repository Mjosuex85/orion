# Template: Issue Estándar

Todo issue creado por Orion debe seguir este formato exacto.

---

## Título
```
tipo: Descripción corta [BACKEND/FRONTEND/FULLSTACK] — Agente
```

Ejemplos:
```
fix: Refresh token no persiste en producción [BACKEND] — Nestor
feature: Componente de FIFA card con stats reales [FRONTEND] — Olga
```

---

## Cuerpo del issue

```markdown
## Contexto
¿Por qué existe este issue? ¿Qué problema resuelve?

## Prompt para [NESTOR/OLGA]
[Instrucciones exactas y detalladas de qué implementar.
Incluir: archivos a modificar, código de referencia si aplica, restricciones.]

## Archivos a modificar
- `ruta/archivo.ts`

## Commit esperado
[AGENTE] tipo(scope): descripción ref #XX | size: S/M/L/XL

## Plan de pruebas para el Director
1. Paso a paso de cómo verificar que funciona
2. Qué debe ver en pantalla / consola
3. Casos de error a probar
```

---

## Tallas de tokens (size)

| Talla | Tokens estimados | Cuándo usarla |
|-------|-----------------|---------------|
| S     | ~1,000          | Bug fix simple, cambio de config, 1-5 líneas |
| M     | ~3,000          | Feature pequeño, refactor, 1 archivo |
| L     | ~8,000          | Feature complejo, múltiples archivos |
| XL    | ~15,000         | Arquitectura, análisis profundo, sistema nuevo |

---

## Labels estándar

| Label | Uso |
|-------|-----|
| `bug` | Algo no funciona |
| `feature` | Funcionalidad nueva |
| `refactor` | Mejora de código sin cambiar comportamiento |
| `devops` | Infraestructura, deploy, CI/CD |
| `arquitectura` | Decisiones de diseño de sistema |
| `nestor` | Asignado a Nestor |
| `olga` | Asignado a Olga |
| `Prioridad: alta` | Sprint actual |
| `Prioridad: media` | Siguiente sprint |
| `Prioridad: baja` | Cuando haya tiempo |
| `future` | Documentado para el futuro |

---

*Parte de Orion OS — seguir este formato garantiza que los agentes tengan el contexto correcto.*
