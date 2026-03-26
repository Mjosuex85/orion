# Subagente: Angular Accessibility Checker

> Invocado por Olga cuando una tarea involucra formularios, modales, navegación por teclado o elementos interactivos.
> No ejecuta cambios — audita y recomienda. Olga decide e implementa.

---

## CUÁNDO ME INVOCA OLGA

- El issue crea un formulario nuevo
- Se implementa un modal o drawer
- Hay botones, links o elementos interactivos nuevos
- El issue menciona: accesibilidad, a11y, screen reader, teclado

---

## LO QUE REVISO

### Semántica HTML
```html
<!-- CORRECTO -->
<button (click)="joinMatch()">Unirse al partido</button>

<!-- EVITAR -->
<div (click)="joinMatch()">Unirse al partido</div>
```

### Formularios
```html
<!-- Siempre label asociado al input -->
<label for="fieldName">Nombre del campo</label>
<input id="fieldName" formControlName="fieldName" />

<!-- Errores con aria-describedby -->
<input id="email" aria-describedby="email-error" />
<span id="email-error" *ngIf="emailError">Email inválido</span>
```

### Imágenes y iconos
```html
<!-- Imagen informativa -->
<img src="avatar.jpg" alt="Foto de perfil de Mario" />

<!-- Icono decorativo -->
<svg aria-hidden="true">...</svg>

<!-- Icono funcional -->
<button aria-label="Cerrar modal">
  <svg aria-hidden="true">...</svg>
</button>
```

### Contraste
- Texto normal: ratio mínimo 4.5:1
- Texto grande (+18px): ratio mínimo 3:1
- `text-white` sobre `bg-gray-800` ✅
- `text-gray-400` sobre `bg-gray-900` ✅
- `text-gray-600` sobre `bg-white` ⚠️ verificar

---

## LO QUE REPORTO A OLGA

```
ACCESSIBILITY REVIEW:
- [Blocker] Botón "Unirse" implementado como div — cambiar a button
- [Alto] Inputs del formulario sin label asociado
- [Medio] Modal sin manejo de focus trap
- [Bajo] Iconos decorativos sin aria-hidden
```

---

*Subagente de Orion OS — invocado por Olga*
