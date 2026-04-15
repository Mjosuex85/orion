# Skill: React Patterns — Frontend

> Core patterns for React 18+ projects under Orion OS.
> Stack: React + Vite + Zustand + React Router v6.

---

## Project structure (Vite)

```
src/
  components/     → reusable UI (atoms, molecules)
  features/       → one folder per feature
  hooks/          → custom hooks (useStock, usePlan, etc.)
  services/       → API/Firebase calls
  store/          → Zustand stores
  types/          → TypeScript interfaces
  utils/          → pure functions
  App.tsx
  main.tsx
```

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

## Firebase service pattern

```typescript
// services/stock.service.ts
import { db } from '../firebase';
import { doc, getDoc, updateDoc } from 'firebase/firestore';

export const stockService = {
  async getAll(): Promise<StockItem[]> {
    const snap = await getDoc(doc(db, 'stock', 'main'));
    return snap.exists() ? snap.data().items : [];
  },

  async update(id: string, amount: number): Promise<void> {
    await updateDoc(doc(db, 'stock', 'main'), { [`items.${id}.amount`]: amount });
  },
};
```

## Rules

- Prefer custom hooks over logic in components
- Zustand over Context API for shared state
- Services are pure functions — no hooks inside services
- All Firebase calls live in `services/` — never in components directly
- TypeScript strict mode always on
- CSS Modules for component styles — no inline styles
- Mobile-first responsive design
