# Race Condition Timeline Visualizer · 競態條件時間序列視覺化工具

> Visualize how race conditions occur when two threads increment a shared counter with non-atomic operations  
> 透過時間序列動畫，直觀理解多執行緒對共享變數 `counter` 做 `counter++` 時為什麼會出錯

這是一個用 **時間軸 + 動畫步驟** 來教學「競態條件 (Race Condition)」的小工具。

畫面中有兩個執行緒（執行緒 0 / 執行緒 1），都在對同一個共享變數 `counter` 做一次 `counter++`。  
理想情況下，最後 `counter` 應該是 `2`，但實際動畫會展示：

- **兩個執行緒都先讀到 0**
- 分別在各自的暫存器裡算 `0 + 1`
- 再輪流把 `1` 寫回去
- 最後 `counter = 1` ➜ 也就是典型的 **lost update（更新遺失）**

這個工具很適合拿來：

- 教學「為什麼 `counter++` 不是原子操作」
- 說明「多執行緒 / 多核心環境下需要 Mutex / Lock 的理由」
- 在課堂 / 公司內訓的時候當 live demo

---

## 🔗 Demo

> https://catdogbird3.github.io/race-condition-timeline/`

---

## ✨ Features · 功能特色

- ⏱ **時間序列動畫呈現**
  - 每一步會亮起對應的方塊（讀取 / 計算 / 寫入）
  - 執行緒 0 / 執行緒 1 分別有獨立軸線，顏色區分

- 📊 **共享記憶體即時顯示**
  - 顯示 `counter` 的目前值
  - 顯示「預期結果 = 2」
  - 當最後結果是 1 時，顯示醒目的錯誤警示訊息

- 🎮 **互動控制**
  - ▶ **開始執行**：自動依序播放六個步驟
  - ⏸ 暫停 / ▶ 繼續：中途可停下再接續
  - 🔄 **重置**：重頭再看一次
  - 「速度」滑桿：快速 / 普通 / 慢速 三種節奏

- 🧠 **文字說明區**
  - 解釋「讀取-修改-寫入」為什麼不是原子操作
  - 條列出每一步的邏輯順序
  - 說明典型 counter++ 競態條件的流程
  - 提醒可以用 Mutex / Semaphore 來解決

---

## 🧠 競態條件概念總結

工具底部的說明重點，可以簡單整理成：

### 問題核心：`counter++` 不是原子操作

`counter++` 通常會被拆成三個動作：

1. 讀取 `counter` 的值
2. 在暫存器中計算 `counter + 1`
3. 把結果寫回 `counter`

這三步在 CPU / 多執行緒環境中**可能被穿插交錯**，所以不是一條不可分割的原子操作。

### 範例流程（本工具模擬的情境）

1. 執行緒 0 讀取 `counter = 0`
2. 執行緒 1 也讀取 `counter = 0`（此時還沒人寫入）
3. 執行緒 0 計算 `0 + 1`，準備寫入
4. 執行緒 1 也計算 `0 + 1`，準備寫入
5. 執行緒 0 寫入 `counter = 1`
6. 執行緒 1 再把自己的結果 `1` 寫回 `counter`

> 最終：`counter = 1`，而不是預期的 `2`  
> 這就是典型的 **競態條件 + 更新遺失 (lost update)**。

### 解法方向（給學生 / 同事的 hint）

- 用 **互斥鎖 (Mutex)** 保護 `counter++`
- 或用 **原子操作 (atomic operations)**  
- 或改用 **訊息佇列 / 工作排程** 避免多執行緒直接共享同一個變數

---

## 🏗 Tech Stack · 技術棧

- **HTML5**：單頁靜態網頁
- **Tailwind CSS（CDN 版）**：
  - 快速做出漸層背景 / 卡片 / 按鈕
- **原生 JavaScript**：
  - 控制步驟動畫（`steps` 陣列 + `playAnimation()`）
  - 控制 `counter` 顯示與警示訊息
  - 綁定按鈕、速度滑桿事件
- **無後端、無打包流程**：
  - 直接開 `index.html` 或 GitHub Pages 就可以用

---

## 📁 專案結構


```bash
.
├─ index.html   # 競態條件時間序列視覺化主頁
└─ README.md    # 專案說明（本檔）
