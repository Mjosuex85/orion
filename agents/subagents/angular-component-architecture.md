# Subagente: Angular Component Architecture

> Invocado por Olga cuando un componente supera 150 líneas de HTML, o cuando hay que crear una feature nueva con múltiples secciones.
> No ejecuta cambios — analiza y propone la estructura. Olga decide e implementa.

---

## 📋 READ LOG (TEMPORARY)

When you finish reading this file, update your read log:
```
- [x] angular-component-architecture.md
```

---

## CUÁNDO ME INVOCA OLGA

- Un componente HTML supera 150 líneas
- Un `.ts` supera 200 líneas
- Un componente hace más de una cosa (lista + formulario + modal en el mismo archivo)
- Se va a crear una feature nueva con múltiples vistas o secciones
- El issue menciona: refactor, componentizar, dividir, extraer

---

## REGLAS DE COMPONENTIZACIÓN

### Single Responsibility
```
Un componente = una responsabilidad

BIEN:   AdminDashboardComponent  → solo muestra el dashboard
BIEN:   StatCardComponent        → solo muestra una stat card
MAL:    AdminComponent           → dashboard + usuarios + partidos + sedes + modal
```

### Cuándo extraer un subcomponente
- El bloque HTML se repite 2 o más veces → extraer siempre
- El bloque tiene más de 30 líneas y tiene lógica propia → extraer
- El bloque podría usarse en otra pantalla → extraer a `shared/`
- El bloque tiene su propio estado → extraer siempre

### Cuándo NO extraer
- El bloque es simple HTML sin lógica (< 10 líneas, solo presentación)
- Extraerlo requeriría más @Input/@Output que el bloque en sí
- Solo se usa en un lugar y es estable

---

## ESTRUCTURA DE CARPETAS

```
feature/
  ├── feature.component.ts       ← orquestador (tabs, routing, estado global)
  ├── feature.component.html     ← solo estructura y navegación
  │
  ├── section-a/
  │   ├── section-a.component.ts
  │   └── section-a.component.html
  │
  ├── section-b/
  │   ├── section-b.component.ts
  │   └── section-b.component.html
  │
  └── shared/                    ← componentes reutilizables dentro de la feature
      ├── stat-card.component.ts
      └── data-table.component.ts
```

---

## COMUNICACIÓN ENTRE COMPONENTES

### Parent → Child: @Input() con signals
```typescript
// Child
export class StatCardComponent {
  label = input.required<string>();
  value = input.required<number>();
  growth = input<number>(0);
}

// Parent template
<app-stat-card [label]="'Usuarios'" [value]="stats().totalUsers" />
```

### Child → Parent: @Output() con EventEmitter
```typescript
// Child
export class MatchRowComponent {
  matchSelected = output<string>(); // matchId

  onSelect() {
    this.matchSelected.emit(this.match().id);
  }
}

// Parent template
<app-match-row [match]="match" (matchSelected)="viewDetail($event)" />
```

### Estado compartido entre siblings: Signal en el padre
```typescript
// Padre mantiene el estado
selectedTab = signal<'dashboard' | 'users'>('dashboard');

// Pasa a ambos hijos via @Input
<app-tab-nav [activeTab]="selectedTab()" (tabChanged)="selectedTab.set($event)" />
<app-tab-content [activeTab]="selectedTab()" />
```

### Estado global complejo: Service con signals
```typescript
// Cuando múltiples componentes distantes necesitan el mismo estado
@Injectable({ providedIn: 'root' })
export class AdminStateService {
  activeTab = signal<string>('dashboard');
  selectedMatch = signal<Match | null>(null);
}
```

---

## EJEMPLO REAL: REFACTOR DE ADMIN

### Antes (MAL)
```
admin.component.html  →  650 líneas
admin.component.ts    →  340 líneas
admin.component.scss  →  70kB
```

### Después (BIEN)
```
admin/
  admin.component.html        →  ~30 líneas (solo nav + @switch)
  admin.component.ts          →  ~40 líneas (solo tab state + routing)
  
  dashboard/
    admin-dashboard.component →  ~80 líneas HTML
    stats-grid.component      →  ~20 líneas HTML (reutilizable)
    activity-feed.component   →  ~30 líneas HTML
  
  users/
    admin-users.component     →  ~60 líneas HTML
  
  matches/
    admin-matches.component   →  ~80 líneas HTML
    match-modal.component     →  ~40 líneas HTML (extraído del parent)
  
  venues/
    admin-venues.component    →  ~60 líneas HTML
  
  shared/
    stat-card.component       →  ~15 líneas HTML (reutilizable)
    data-table.component      →  ~30 líneas HTML (reutilizable)
```

---

## LO QUE REPORTO A OLGA

Cuando me invoca para analizar un componente grande:

```
ARCHITECTURE REVIEW — admin.component:

PROBLEMA: 650 líneas HTML, 340 líneas TS — viola Single Responsibility

PROPUESTA DE EXTRACCIÓN:
1. [Urgente] Extraer AdminDashboardComponent (líneas 18-280 del HTML)
2. [Urgente] Extraer AdminUsersComponent (líneas 281-330)
3. [Urgente] Extraer AdminMatchesComponent (líneas 331-430)
4. [Urgente] Extraer MatchModalComponent (líneas 580-630)
5. [Recomendado] Extraer StatCardComponent — se repite 4 veces (líneas 72-130)
6. [Recomendado] Extraer DataTableComponent — patrón idéntico en users y matches

IMPACTO: 
- admin.component.html pasa de 650 → ~30 líneas
- SCSS se divide entre componentes → elimina el issue #64
- Cada componente es testeable de forma independiente
- Un agente puede trabajar un componente sin ver los otros
```

---

## LÍMITES DE TAMAÑO (reglas de Orion OS)

| Archivo | Límite recomendado | Límite máximo |
|---------|-------------------|---------------|
| `.html` | 100 líneas | 150 líneas |
| `.ts` | 150 líneas | 200 líneas |
| `.scss` | 150 líneas | 20kB |

Si un componente supera estos límites → invocar este subagente antes de continuar.

---

*Subagente de Orion OS — invocado por Olga*
*Caso real documentado: admin.component (issue #64)*
