# GameOn — Contexto de Proyecto

> Este archivo le da a Orion y a los agentes el contexto específico de GameOn.
> GameOn es el proyecto laboratorio donde se prueba y refina Orion OS.

---

## QUÉ ES GAMEON

GameOn es una plataforma de **identidad del jugador** para deporte amateur. El diferencial no es gestionar partidos — es que cuando alguien toca tu spot en la cancha, aparece tu FIFA card con foto, país, historia y stats.

**El pitch correcto:** "Reemplaza el WhatsApp y el papel para organizar partidos."

### Las dos capas del negocio
```
Capa gratuita (gancho social)
  └ Crear partidos, perfil, cancha táctica, FIFA card

Capa de pago (monetización)
  └ Organizadores/empresas gestionan ligas y torneos
  └ Jugadores desbloquean stats reales
```

---

## STACK

### Backend — `Mjosuex85/gameon-api`
```
Runtime:    Node.js + NestJS 10
ORM:        TypeORM + PostgreSQL (Neon)
Auth:       JWT (access 15min, refresh 7d) + Google OAuth
Deploy:     Vercel (serverless)
DB:         Neon PostgreSQL (producción)
DB local:   Docker puerto 5434
```

### Frontend — `Mjosuex85/gameon`
```
Framework:  Angular 17+ (100% standalone components)
Estilos:    TailwindCSS + SCSS
State:      Angular Signals
Deploy:     Vercel
```

---

## REGLAS ESPECÍFICAS DE GAMEON

- Todos los issues viven en `gameon-api` — incluyendo los del frontend
- GitHub Project: "GameOn" — Kanban con columnas Todo / In Progress / Done
- Migraciones se corren manualmente antes de cada deploy (ver issue #65)
- `migrationsRun: false` en local, `true` en producción (no confiable en serverless — correr manual)
- Comando de migración en producción (PowerShell):
  ```powershell
  $env:DATABASE_URL="tu_url_neon"; npm run db:migrate
  ```

---

## ESTADO — 26 de marzo de 2026

- ✅ Backend en producción (Vercel)
- ✅ Frontend en producción (Vercel)
- ✅ Migraciones ejecutadas (roles, ciudades, precio)
- ✅ Refresh token funcionando con email/password
- 🔴 Refresh token Google OAuth falla en producción (issue #66)
- 🔴 admin.component.scss sobredimensionado (issue #64)

---

## PRIMER USUARIO OBJETIVO

Organizador de comunidad de fútbol en Madrid.
Hoy usa WhatsApp + papel. GameOn lo reemplaza.

---

*Parte de Orion OS — actualizado el 26 de marzo de 2026*
