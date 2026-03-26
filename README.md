# Orion OS

> El sistema operativo para equipos de desarrollo con IA.

---

## ¿Qué es Orion OS?

Orion OS es un framework para construir y orquestar equipos de agentes IA que desarrollan software real. No es un chatbot — es una metodología de trabajo con roles, reglas, memoria y automatización.

El equipo trabaja como una agencia de desarrollo: cada agente tiene un rol especializado, sigue reglas claras, y se comunica a través de GitHub Issues como capa de estado.

## La jerarquía

```
Director / Founder (visión y decisiones de negocio)
  └── Orion (CTO / orquestador permanente)
        └── Por proyecto:
              ├── Nestor Senior (tech lead backend)
              │     ├── Dev backend
              │     ├── Tester
              │     └── Seguridad
              └── Olga Senior (tech lead frontend)
                    ├── Dev frontend
                    └── QA
```

## Estructura del repo

```
orion/
  ├── README.md              → Este archivo
  ├── ORION.md               → Identidad y memoria global de Orion
  ├── DECISIONS.md           → Reglas globales (todos los proyectos)
  ├── agents/
  │   ├── NESTOR.md          → System prompt de Nestor (tech lead backend)
  │   └── OLGA.md            → System prompt de Olga (tech lead frontend)
  ├── projects/
  │   └── gameon.md          → Contexto específico de GameOn (proyecto lab)
  ├── templates/
  │   ├── new-project.md     → Cómo arrancar un proyecto nuevo
  │   └── issue-template.md  → Formato estándar de issues
  └── workflows/
      └── commit-log.yml     → GitHub Action para métricas de actividad
```

## Cómo usar Orion OS en un proyecto nuevo

1. Copiar `templates/new-project.md` y rellenar el contexto del proyecto
2. Crear los repos del proyecto con `AGENTS.md` y `DECISIONS.md`
3. Configurar GitHub Project (Kanban): Todo / In Progress / Done
4. Dar a cada agente su system prompt de `agents/` + el contexto del proyecto
5. Orion coordina — los agentes ejecutan — el Director valida

## Escalabilidad

Orion OS crece por fases:

```
Fase 1 → Framework manual + acompañamiento (hoy)
Fase 2 → Claude API como motor de orquestación semi-automática
Fase 3 → Sistema completamente automatizado con supervisión humana
```

---

*Creado por Mario Vidal + Orion — 26 de marzo de 2026*
*GameOn es el proyecto laboratorio donde se prueba y refina este framework.*
