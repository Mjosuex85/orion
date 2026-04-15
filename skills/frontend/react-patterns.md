# Skill: React Patterns — Frontend

> Core patterns for React 18+ projects under Orion OS.
> Provider-agnostic: service layer abstracts the data source (Supabase, REST API, etc.)
> Stack: React + Vite + Zustand + React Router v6.

---

## Project structure (Vite)

```
src/
  components/     → reusable UI (atoms, molecules)
  features/       → one folder per feature
  hooks/          → custom hooks (useStock, usePlan, etc.)
  services/       → data access layer (abstracts provider)
  store/          → Zustand stores
  types/          → TypeScript interfaces
  utils/          → pure functions
  App.tsx
  main.tsx
```

---

## State management — Zustand

```typescript
import { create } from 'zustand';

interface StockState {
  items: StockItem[];
  loading: boolean;
  fetchStock: () => Promise<void>;
}

export const useStockStore = create<StockState>((set) => ({
  items: [],
  loading: false,
  fetchStock: async () => {
    set({ loading: true });
    const data = await stockService.getAll();
    set({ items: data, loading: false });
  },
}));
```

---

## Custom hooks pattern

```typescript
// hooks/useStock.ts
export function useStock() {
  const { items, loading, fetchStock } = useStockStore();

  useEffect(() => {
    fetchStock();
  }, []);

  return { items, loading };
}
```

---

## Component pattern

```typescript
// features/stock/StockCard.tsx
interface Props {
  item: StockItem;
  onUpdate?: (id: string, amount: number) => void;
}

export function StockCard({ item, onUpdate }: Props) {
  return (
    <div className={styles.card}>
      <h3>{item.name}</h3>
      <span>{item.amount} {item.unit}</span>
    </div>
  );
}
```

---

## Service layer pattern (provider-agnostic)

Services abstract the data provider. The rest of the app never imports Supabase, Firebase, or any SDK directly.

```typescript
// services/stock.service.ts
// The implementation below uses Supabase — swap for any other provider without touching hooks or components.

import { supabase } from '../lib/supabase';

export const stockService = {
  async getAll(): Promise<StockItem[]> {
    const { data, error } = await supabase.from('stock').select('*');
    if (error) throw error;
    return data ?? [];
  },

  async update(id: string, amount: number): Promise<void> {
    const { error } = await supabase
      .from('stock')
      .update({ amount })
      .eq('id', id);
    if (error) throw error;
  },
};
```

---

## Rules

- All data provider calls live in `services/` — never in components or hooks directly
- Hooks consume services. Components consume hooks.
- Zustand over Context API for shared state
- Services are pure functions — no React hooks inside services
- TypeScript strict mode always on
- CSS Modules for component styles — no inline styles
- Mobile-first responsive design
- `lib/` folder holds provider client initialization (e.g. `lib/supabase.ts`)
