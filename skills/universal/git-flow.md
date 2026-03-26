# Skill: Git Flow — Universal

> Este skill aplica a TODOS los proyectos y TODOS los agentes.

---

## Reglas absolutas

- `main` NO SE TOCA — excepto en deploys planificados
- TODO el trabajo va a `develop`
- Siempre `git pull origin develop` ANTES de empezar
- NUNCA usar `closes #XX` — solo `ref #XX`
- NUNCA hacer commit sin que el Director haya probado

## Formato de commit obligatorio

```
[AGENTE] tipo(scope): descripción ref #XX | size: S/M/L/XL
```

Ejemplos válidos:
```
[NESTOR] fix(auth): cookie path corrected ref #42 | size: S
[OLGA] feat(profile): add avatar upload component ref #53 | size: M
[NESTOR] refactor(matches): optimize N+1 queries ref #55 | size: L
```

## Tipos de commit

| Tipo | Uso |
|------|-----|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección de bug |
| `refactor` | Mejora sin cambiar comportamiento |
| `style` | Cambios de estilos/formato |
| `docs` | Documentación |
| `chore` | Configuración, dependencias |

## Tallas (size)

| Talla | Tokens | Cuándo |
|-------|--------|--------|
| S | ~1,000 | 1-5 líneas, config |
| M | ~3,000 | 1 archivo, feature pequeño |
| L | ~8,000 | Múltiples archivos |
| XL | ~15,000 | Arquitectura, sistema nuevo |
