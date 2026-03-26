# Template: Nuevo Proyecto

Usa este template para arrancar cualquier proyecto nuevo con Orion OS en menos de 1 hora.

---

## 1. Definir el proyecto

```
Nombre del proyecto:
¿Qué problema resuelve?
Pitch en una oración:
Tipo de usuarios:
Primer usuario objetivo:
Stack backend:
Stack frontend:
DB:
Deploy:
```

---

## 2. Checklist de infraestructura

- [ ] Repo backend: `owner/nombre-api`
- [ ] Repo frontend: `owner/nombre`
- [ ] GitHub Project (Kanban): Todo / In Progress / Done
- [ ] Crear `projects/nombre.md` en Orion OS con el contexto del proyecto
- [ ] Copiar `AGENTS.md` base al repo del proyecto
- [ ] Copiar `DECISIONS.md` base al repo del proyecto y adaptar reglas específicas
- [ ] Configurar variables de entorno en `.env.example`

---

## 3. Configurar agentes

- [ ] Nestor recibe: `agents/NESTOR.md` + `projects/nombre.md`
- [ ] Olga recibe: `agents/OLGA.md` + `projects/nombre.md`
- [ ] Confirmar que ambos entienden el stack y las reglas

---

## 4. Primer issue

Orion crea el issue #1 con este formato:

```markdown
## Contexto
Setup inicial del proyecto — estructura de carpetas, configuración base, primer endpoint/componente.

## Prompt para [NESTOR/OLGA]
[Instrucciones específicas del setup inicial]

## Plan de pruebas para el Director
1. El proyecto arranca sin errores
2. El primer endpoint/componente responde correctamente
3. Variables de entorno documentadas en .env.example
```

---

## 5. Reglas específicas del proyecto

Completar en `projects/nombre.md`:
- Stack completo
- Reglas específicas que difieren de las globales
- Variables de entorno necesarias
- Flujo de deploy
- Estado inicial

---

*Parte de Orion OS — usar este template garantiza consistencia entre proyectos.*
