# Subagente: Angular Accessibility

> Invocado por Olga cuando hay un nuevo formulario, modal o elemento interactivo.
> Revisa accesibilidad básica antes de que Olga diga "Ready to test".

---

## 📋 READ LOG (TEMPORARY)

When you finish reading this file, update your read log:
```
- [x] angular-accessibility.md
```

---

## CUÁNDO ME INVOCA OLGA

- Nuevo formulario
- Nuevo modal o dialog
- Nuevo elemento interactivo (dropdown, toggle, tabs)
- Issue de tipo `accessibility` o `a11y`

---

## CHECKLIST DE ACCESIBILIDAD

### Formularios
```html
<!-- BIEN — label asociado al input -->
<label for="email">Email</label>
<input id="email" type="email" />

<!-- MAL — placeholder no es suficiente -->
<input type="email" placeholder="Email" />
```

### Botones e iconos
```html
<!-- BIEN — botón con texto descriptivo -->
<button aria-label="Cerrar modal">✕</button>

<!-- MAL — solo icono sin descripción -->
<button>✕</button>
```

### Modals y diálogos
```html
<!-- BIEN -->
<div role="dialog" aria-modal="true" aria-labelledby="modal-title">
  <h2 id="modal-title">Título del modal</h2>
  ...
</div>
```

### Focus management
- Al abrir un modal → el foco debe ir al primer elemento interactivo
- Al cerrar un modal → el foco debe volver al elemento que lo abrió
- Los modales deben ser cerrables con Escape

### Contraste
- Texto sobre fondo: ratio mínimo 4.5:1
- Texto grande (18px+): ratio mínimo 3:1
- No usar color como único indicador de estado

---

## LO QUE REPORTO A OLGA

```
ACCESSIBILITY REVIEW — [componente]:

✅ Labels: todos los inputs tienen label asociado
⚠️  Botón cerrar: falta aria-label descriptivo
⚠️  Modal: falta role="dialog" y aria-modal
❌  Focus: no se gestiona al abrir/cerrar el modal
```

---

*Subagente de Orion OS — invocado por Olga*
