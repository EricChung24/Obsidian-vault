---
tags: [log, system]
---

# Log — 知識庫動作紀錄

這份檔案紀錄 Claude 對 vault 做過的結構性動作（搬移、合併、建立新頁、批次修正等）。
目的是讓未來的你（或未來的 Claude）可以追溯「誰在什麼時候改了什麼」。

格式：每筆紀錄用一個 `## YYYY-MM-DD` 區塊，條列當天動作與原因。

---

## 2026-04-19

- 導入 Karpathy 風格知識庫架構：建立 `raw/`、`wiki/`、`index/`、`log/` 四個頂層資料夾。
- 將原本 12 個編號主題資料夾（`01-基礎技術` ~ `12-面試準備`）整批搬到 `wiki/` 底下。
- 將 `前端工程師.md`（MOC 知識地圖）搬到 `index/`。
- `docs/`、`.obsidian/`、`.claude/` 保留在根目錄不動。
- 更新 `.claude/CLAUDE.md`，改為 Karpathy 版本：新增 ingest 流程、health check 流程、log 規則。
- 原因：要建立可長期維護的知識庫工作流，取代過去單純依主題分類的資料夾結構。
- 未更新 Obsidian wiki link：Obsidian 是用檔名比對而非路徑，內部 `[[...]]` 連結不會壞。
- 未 commit：等使用者確認新結構後再建立 commit。

---

- 健康檢查結果：無孤立頁、無缺頁、無薄頁面。
- 新增 4 頁（缺口補充 + 比較頁）：
  - `03-狀態管理/狀態管理比較.md`（Redux vs Zustand vs Pinia 選型）
  - `10-無障礙與資安/WCAG與a11y實踐.md`（WCAG 2.1 AA checklist + 實作範例）
  - `06-測試/元件測試實戰.md`（React Testing Library 實戰）
  - `08-瀏覽器與Web-APIs/Fetch API vs Axios.md`（功能對比 + 選型建議）
- 修正 3 頁重疊問題：在 `React效能優化`、`渲染優化`、`React渲染與重渲染` 各加入「本頁範圍」說明，明確分工邊界。
- 原因：健康檢查發現 10-無障礙/資安（2頁）、03-狀態管理（3頁）、06-測試（3頁）為最薄區塊；三個效能頁面主題重疊待釐清。
