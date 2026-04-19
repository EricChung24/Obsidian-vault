---
tags: [React, Hooks, 設計模式]
created: 2026-04-16
---

# React Hooks 進階模式

> 常見 Custom Hook 模式，解決典型前端問題。

← [[前端工程師]]

---

## useLocalStorage

```ts
function useLocalStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setValue = (value: T | ((prev: T) => T)) => {
    try {
      const newValue = value instanceof Function ? value(storedValue) : value;
      setStoredValue(newValue);
      localStorage.setItem(key, JSON.stringify(newValue));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue] as const;
}

// 使用
const [theme, setTheme] = useLocalStorage<'light' | 'dark'>('theme', 'light');
```

---

## useDebounce

```ts
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// 使用：搜尋框防抖
function SearchBar() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);

  // 只有 debouncedQuery 變化才發請求
  const { data } = useQuery({
    queryKey: ['search', debouncedQuery],
    queryFn: () => api.get(`/search?q=${debouncedQuery}`),
    enabled: debouncedQuery.length > 1,
  });

  return <input value={query} onChange={e => setQuery(e.target.value)} />;
}
```

---

## useIntersectionObserver

```ts
function useIntersectionObserver(
  options: IntersectionObserverInit = {}
) {
  const [isIntersecting, setIsIntersecting] = useState(false);
  const ref = useRef<Element>(null);

  useEffect(() => {
    const element = ref.current;
    if (!element) return;

    const observer = new IntersectionObserver(
      ([entry]) => setIsIntersecting(entry.isIntersecting),
      options
    );

    observer.observe(element);
    return () => observer.disconnect();
  }, [options.threshold, options.rootMargin]);

  return [ref, isIntersecting] as const;
}

// 使用：進入視口時觸發動畫
function AnimatedCard({ children }) {
  const [ref, isVisible] = useIntersectionObserver({ threshold: 0.1 });

  return (
    <div
      ref={ref}
      className={`card ${isVisible ? 'animate-fadeIn' : 'opacity-0'}`}
    >
      {children}
    </div>
  );
}
```

---

## useEventListener

```ts
function useEventListener<K extends keyof WindowEventMap>(
  eventType: K,
  handler: (event: WindowEventMap[K]) => void,
  element: EventTarget = window,
  options?: AddEventListenerOptions
) {
  const handlerRef = useRef(handler);
  handlerRef.current = handler; // 永遠用最新的 handler

  useEffect(() => {
    const fn = (event: Event) => handlerRef.current(event as WindowEventMap[K]);
    element.addEventListener(eventType, fn, options);
    return () => element.removeEventListener(eventType, fn, options);
  }, [eventType, element]);
}

// 使用
function App() {
  useEventListener('keydown', (e) => {
    if (e.key === 'Escape') closeModal();
  });

  useEventListener('resize', () => {
    updateLayout(window.innerWidth);
  });
}
```

---

## useAsync

```ts
type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };

function useAsync<T>() {
  const [state, setState] = useState<AsyncState<T>>({ status: 'idle' });

  const execute = useCallback(async (promise: Promise<T>) => {
    setState({ status: 'loading' });
    try {
      const data = await promise;
      setState({ status: 'success', data });
      return data;
    } catch (error) {
      setState({ status: 'error', error: error as Error });
      throw error;
    }
  }, []);

  const reset = useCallback(() => setState({ status: 'idle' }), []);

  return { ...state, execute, reset };
}

// 使用
function SubmitForm() {
  const { status, error, execute } = useAsync<{ id: number }>();

  const handleSubmit = () => {
    execute(api.post('/submit', formData));
  };

  return (
    <>
      <button onClick={handleSubmit} disabled={status === 'loading'}>
        {status === 'loading' ? '提交中...' : '提交'}
      </button>
      {status === 'error' && <p className="error">{error.message}</p>}
    </>
  );
}
```

---

## useMediaQuery

```ts
function useMediaQuery(query: string): boolean {
  const [matches, setMatches] = useState(
    () => window.matchMedia(query).matches
  );

  useEffect(() => {
    const mq = window.matchMedia(query);
    const handler = (e: MediaQueryListEvent) => setMatches(e.matches);
    mq.addEventListener('change', handler);
    return () => mq.removeEventListener('change', handler);
  }, [query]);

  return matches;
}

// 使用
function ResponsiveNav() {
  const isMobile = useMediaQuery('(max-width: 768px)');
  const prefersDark = useMediaQuery('(prefers-color-scheme: dark)');

  return isMobile ? <MobileNav /> : <DesktopNav />;
}
```

---

## 相關連結

- [[React]] — Hooks 基礎
- [[元件設計模式]] — 元件架構
