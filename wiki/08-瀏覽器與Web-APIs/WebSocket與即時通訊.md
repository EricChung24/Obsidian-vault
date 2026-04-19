# WebSocket 與即時通訊

## Summary
WebSocket 是一種在瀏覽器與伺服器之間建立**持久雙向連線**的協議，讓伺服器可以主動推送資料，不需要客戶端輪詢。

## Plain Explanation
傳統 HTTP 就像「寄信」：你寄一封，對方回一封，然後連線就斷了。

WebSocket 像「打電話」：連線一旦建立，雙方可以隨時說話，不需要重新撥號。

適合用在：即時聊天、線上遊戲、股票行情、協作編輯（如 Notion、Figma）。

## Technical Definition

### 連線建立流程（HTTP Upgrade）
```
客戶端發送 HTTP Upgrade 請求：
GET /chat HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZQ==

伺服器回應：
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
```
握手後，協議從 HTTP 升級為 WebSocket（`ws://` 或 `wss://`），連線持續開啟。

### 與 HTTP Polling 的比較

| 方式 | 描述 | 缺點 |
|------|------|------|
| Short Polling | 每隔幾秒發一次請求 | 浪費頻寬，延遲高 |
| Long Polling | 請求掛起直到有新資料 | 複雜，連線資源消耗大 |
| SSE | 伺服器單向推送 | 只能單向（伺服器→客戶端） |
| WebSocket | 雙向即時通訊 | 需要持久連線，伺服器負載較高 |

## Example

### 原生 WebSocket API
```typescript
// 建立連線
const socket = new WebSocket('wss://example.com/socket');

// 連線成功
socket.addEventListener('open', () => {
  console.log('已連線');
  socket.send(JSON.stringify({ type: 'join', room: 'general' }));
});

// 接收訊息
socket.addEventListener('message', (event) => {
  const data = JSON.parse(event.data);
  console.log('收到訊息:', data);
});

// 連線關閉
socket.addEventListener('close', (event) => {
  console.log('連線關閉', event.code, event.reason);
});

// 錯誤處理
socket.addEventListener('error', (error) => {
  console.error('WebSocket 錯誤', error);
});

// 主動關閉連線
socket.close();
```

### 在 React 中封裝成 Custom Hook
```tsx
import { useEffect, useRef, useState, useCallback } from 'react';

function useWebSocket(url: string) {
  const socketRef = useRef<WebSocket | null>(null);
  const [messages, setMessages] = useState<string[]>([]);
  const [isConnected, setIsConnected] = useState(false);

  useEffect(() => {
    const socket = new WebSocket(url);
    socketRef.current = socket;

    socket.onopen = () => setIsConnected(true);
    socket.onclose = () => setIsConnected(false);
    socket.onmessage = (event) => {
      setMessages((prev) => [...prev, event.data]);
    };

    return () => {
      socket.close();
    };
  }, [url]);

  const sendMessage = useCallback((msg: string) => {
    socketRef.current?.send(msg);
  }, []);

  return { messages, isConnected, sendMessage };
}

// 使用範例
function ChatRoom() {
  const { messages, isConnected, sendMessage } = useWebSocket('wss://chat.example.com');

  return (
    <div>
      <p>狀態: {isConnected ? '已連線' : '已斷線'}</p>
      {messages.map((msg, i) => <p key={i}>{msg}</p>)}
      <button onClick={() => sendMessage('Hello!')}>送出</button>
    </div>
  );
}
```

### 使用 Socket.IO（主流函式庫）
```typescript
// 安裝：npm install socket.io-client

import { io } from 'socket.io-client';

const socket = io('https://example.com', {
  autoConnect: true,
  reconnection: true,       // 自動重連
  reconnectionAttempts: 5,
  reconnectionDelay: 1000,
});

// 監聽事件
socket.on('connect', () => console.log('connected:', socket.id));
socket.on('chat:message', (data) => console.log(data));

// 發送事件
socket.emit('chat:send', { text: 'Hello!' });

// 斷開
socket.disconnect();
```

## Server-Sent Events (SSE) — 單向推送的替代方案

SSE 比 WebSocket 簡單，適合**只需要伺服器推送**的場景（如通知、進度條、AI 串流回應）。

```typescript
// 客戶端
const eventSource = new EventSource('/api/stream');

eventSource.onmessage = (event) => {
  console.log('新資料:', event.data);
};

eventSource.onerror = () => {
  eventSource.close();
};

// 伺服器端（Node.js/Express）
app.get('/api/stream', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');

  const interval = setInterval(() => {
    res.write(`data: ${JSON.stringify({ time: Date.now() })}\n\n`);
  }, 1000);

  req.on('close', () => clearInterval(interval));
});
```

## Common Mistakes

### 1. 忘記在組件卸載時關閉連線
```tsx
// 錯誤：每次組件重新渲染都創建新連線
useEffect(() => {
  const socket = new WebSocket(url);
  // 沒有 cleanup！
});

// 正確：清理函數關閉連線
useEffect(() => {
  const socket = new WebSocket(url);
  return () => socket.close(); // cleanup
}, [url]);
```

### 2. 沒有處理斷線重連
網路不穩時 WebSocket 會斷線，需要自動重連邏輯。
推薦直接使用 Socket.IO 或 reconnecting-websocket 函式庫。

### 3. 在 URL 中傳遞敏感 token
WebSocket URL 可能被記錄在 log 中，token 應放在連線後的第一個訊息，或用 cookie。

### 4. 忘記 `wss://` (TLS)
生產環境必須用 `wss://`（加密），就像 HTTP → HTTPS。

## 應用場景選擇指南

| 場景 | 推薦方案 |
|------|---------|
| 即時聊天、多人協作 | WebSocket / Socket.IO |
| 伺服器通知、進度更新 | SSE |
| AI 串流回應（如 ChatGPT） | SSE |
| 簡單資料更新（不需即時） | Polling 或 React Query refetchInterval |
| 線上遊戲 | WebSocket（低延遲） |

## Related Concepts
- [[HTTP協議]]
- [[網路協定]]
- [[Fetch API]]
- [[React Query]]
- [[前端監控]]

## Source Notes
- MDN WebSocket API
- Socket.IO 官方文件
