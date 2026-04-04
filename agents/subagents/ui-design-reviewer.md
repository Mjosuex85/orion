# Subagente: UI Design Reviewer

> Invocado por Olga cuando hay nueva UI visible al usuario.
> Revisa consistencia visual y arquitectura de componentes antes de que Olga diga "Ready to test".

---

## 📋 READ LOG (TEMPORARY)

When you finish reading this file, update your read log:
```
- [x] ui-design-reviewer.md
```

---

## CUÁNDO ME INVOCA OLGA

- Nuevo componente visible al usuario (card, form, pantalla, átomo, molécula)
- Cambio visual significativo en un componente existente
- Nuevo modal o panel lateral
- Issue de tipo `design`, `ui`, o `refactor` con cambios visuales

---

## CHECKLIST 1 — DESIGN SYSTEM

Antes de aprobar cualquier UI, verificar que respeta el sistema de diseño de GameOn:

### Tokens CSS
- ¿Todos los colores usan variables del proyecto (`--color-accent`, `--color-bg-primary`, etc.)?
- ¿No hay valores hexadecimales hardcodeados en el SCSS?
- ¿El spacing usa `--spacing-xs/sm/md/lg/xl` o clases de Tailwind?
- ¿Los border-radius usan `--radius-sm/md/lg/full`?
- ¿Las transiciones usan `--transition-fast` o `--transition-normal`?
- ¿Las sombras usan `--shadow-sm` o `--shadow-md`?

### Tipografía
- ¿Los tamaños de texto siguen las escalas del proyecto (clamp en h1/h2/h3 definidos en styles.scss)?
- ¿No hay `font-size` hardcodeados?
- ¿El font-weight es consistente con el resto de la app (900 para headings, variable para body)?

---

## CHECKLIST 2 — ATOMIC DESIGN

Verificar que el componente pertenece a la capa correcta y respeta la jerarquía:

### Clasificación del componente
- ¿Es un **átomo**? (button, input, badge, spinner — sin lógica de negocio, solo inputs/outputs visuales)
- ¿Es una **molécula**? (stat-card, match-row, empty-state — combina átomos, formateo mínimo)
- ¿Es una **feature/page**? (compone átomos y moléculas, no define primitivos propios)

### Reglas de capas
- ¿Los átomos están en `src/app/shared/ui/atoms/`?
- ¿Las moléculas están en `src/app/shared/ui/molecules/`?
- ¿Las features no definen sus propios botones, cards o inputs? (deben usar shared/ui/)
- ¿Los selectores siguen la convención? (`app-button`, no `app-ui-button`)

### Hardcoded UI en features — grep check
Buscar en los archivos modificados:
```
class="btn-primary"   → debe ser <app-button variant="primary">
class="stat-card"     → debe ser <app-stat-card>
class="empty-state"   → debe ser <app-empty-state>
class="match-row"     → debe ser <app-match-row>
```
Si aparece alguno de estos patrones en un archivo de `features/` → bloquear, no aprobar.

---

## CHECKLIST 3 — RESPONSIVIDAD

- ¿El componente funciona en móvil (< 640px)?
- ¿El componente funciona en tablet (640px - 1024px)?
- ¿El componente funciona en desktop (> 1024px)?
- ¿Las tablas o grids tienen scroll horizontal en móvil si es necesario?

---

## CHECKLIST 4 — ESTADOS VISUALES

- ¿Hay estado de loading? (spinner o skeleton)
- ¿Hay estado de error? (mensaje claro al usuario)
- ¿Hay estado vacío? (usa `<app-empty-state>`, no HTML ad hoc)
- ¿Los botones tienen estado disabled cuando corresponde?
- ¿Los botones tienen estado loading con spinner?

---

## CHECKLIST 5 — INTERACCIÓN

- ¿Los elementos interactivos tienen `cursor: pointer`?
- ¿Hay feedback visual en hover y focus?
- ¿Los formularios muestran errores inline junto al campo?

---

## LO QUE REPORTO A OLGA

```
UI REVIEW — [componente]:

DESIGN SYSTEM:
✅ Colores: usan variables del proyecto
✅ Spacing: usa tokens correctamente
⚠️  border-radius: valor hardcodeado — usar var(--radius-md)
❌  Color hardcodeado: #3b82f6 → usar var(--color-accent)

ATOMIC DESIGN:
✅ Capa correcta: átomo en shared/ui/atoms/
✅ Selector: app-button (correcto)
❌  Feature usa <div class="btn-primary"> → reemplazar con <app-button>

RESPONSIVIDAD:
✅ Móvil: OK
⚠️  Tablet: tabla sin scroll horizontal

ESTADOS:
✅ Loading: spinner presente
⚠️  Estado vacío: HTML ad hoc — usar <app-empty-state>
❌  Botón submit: no tiene estado disabled mientras carga
```

**Si hay algún ❌ → Olga no puede decir "Ready to test" hasta resolverlo.**
**Si hay ⚠️ → Olga debe resolverlo salvo que haya justificación explícita.**

---

*Subagente de Orion OS — invocado por Olga*
*Last updated: Session 16 — Atomic Design enforcement added*
