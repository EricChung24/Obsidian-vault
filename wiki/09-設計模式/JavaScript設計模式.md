---
tags: [設計模式, JavaScript, 架構]
created: 2026-04-16
---

# JavaScript 設計模式

> 解決特定問題的可重用設計方案。

← [[前端工程師]]

---

## 創建型模式

### 單例模式（Singleton）

```ts
class ConfigManager {
  private static instance: ConfigManager;
  private config: Record<string, string> = {};

  private constructor() {}  // 私有建構子

  static getInstance(): ConfigManager {
    if (!ConfigManager.instance) {
      ConfigManager.instance = new ConfigManager();
    }
    return ConfigManager.instance;
  }

  set(key: string, value: string) { this.config[key] = value; }
  get(key: string) { return this.config[key]; }
}

// 使用
const config = ConfigManager.getInstance();
config.set('apiUrl', 'https://api.example.com');
```

### 工廠模式（Factory）

```ts
interface Logger {
  log(message: string): void;
}

class ConsoleLogger implements Logger {
  log(msg: string) { console.log(`[Console] ${msg}`); }
}

class FileLogger implements Logger {
  log(msg: string) { writeFile('app.log', msg); }
}

function createLogger(type: 'console' | 'file'): Logger {
  const loggers = {
    console: () => new ConsoleLogger(),
    file:    () => new FileLogger(),
  };
  return loggers[type]();
}
```

---

## 結構型模式

### 觀察者模式（Observer）

```ts
type Listener<T> = (payload: T) => void;

class EventEmitter<Events extends Record<string, any>> {
  private listeners = new Map<keyof Events, Set<Listener<any>>>();

  on<K extends keyof Events>(event: K, listener: Listener<Events[K]>) {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, new Set());
    }
    this.listeners.get(event)!.add(listener);
    return () => this.off(event, listener);  // 回傳取消訂閱函式
  }

  off<K extends keyof Events>(event: K, listener: Listener<Events[K]>) {
    this.listeners.get(event)?.delete(listener);
  }

  emit<K extends keyof Events>(event: K, payload: Events[K]) {
    this.listeners.get(event)?.forEach(fn => fn(payload));
  }
}

// 使用
type AppEvents = {
  userLogin: { userId: string };
  cartUpdate: { itemCount: number };
};

const emitter = new EventEmitter<AppEvents>();
const unsubscribe = emitter.on('userLogin', ({ userId }) => {
  console.log(`用戶 ${userId} 登入`);
});
```

### 裝飾器模式（Decorator）

```ts
// 函式式裝飾器
function withLogging<T extends (...args: any[]) => any>(fn: T): T {
  return ((...args) => {
    console.log(`呼叫 ${fn.name}，參數:`, args);
    const result = fn(...args);
    console.log(`${fn.name} 回傳:`, result);
    return result;
  }) as T;
}

function withRetry<T extends (...args: any[]) => Promise<any>>(
  fn: T,
  maxRetries = 3
): T {
  return (async (...args) => {
    for (let i = 0; i <= maxRetries; i++) {
      try {
        return await fn(...args);
      } catch (err) {
        if (i === maxRetries) throw err;
        await new Promise(r => setTimeout(r, 2 ** i * 100));
      }
    }
  }) as T;
}
```

---

## 行為型模式

### 策略模式（Strategy）

```ts
type SortStrategy = (arr: number[]) => number[];

const strategies: Record<string, SortStrategy> = {
  bubble: (arr) => { /* 泡沫排序 */ return arr; },
  quick:  (arr) => [...arr].sort((a, b) => a - b),
  merge:  (arr) => { /* 合併排序 */ return arr; },
};

function sortData(arr: number[], strategy: keyof typeof strategies) {
  return strategies[strategy](arr);
}
```

### 命令模式（Command）— 支援 Undo/Redo

```ts
interface Command {
  execute(): void;
  undo(): void;
}

class CommandHistory {
  private history: Command[] = [];
  private index = -1;

  execute(cmd: Command) {
    this.history.splice(this.index + 1);
    this.history.push(cmd);
    this.index++;
    cmd.execute();
  }

  undo() {
    if (this.index >= 0) {
      this.history[this.index--].undo();
    }
  }

  redo() {
    if (this.index < this.history.length - 1) {
      this.history[++this.index].execute();
    }
  }
}
```

---

## 相關連結

- [[JavaScript]] — 原型鏈
- [[元件設計模式]] — React 特有模式
