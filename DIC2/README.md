# Value Iteration - 最佳政策推導視覺化 (Grid World) 🗺️🚀

這是一個用來視覺化呈現**價值迭代算法 (Value Iteration)** 在 5×5 網格世界 (Grid World) 中推導最佳政策的互動式網頁應用。透過本專案，您可以直觀地看到每個狀態的價值如何透過貝爾曼方程式 (Bellman Equation) 進行更新，並最終收斂出抵達終點的最佳路徑。

## 🌟 網頁連結

👉 **[Demo page (https://pcchou102.github.io/DRL_DIC2/)](https://pcchou102.github.io/DRL_DIC2/)**

---

## 🛠️ 完整的實作流程

本專案主要使用 HTML, CSS 與 Vanilla JavaScript 來建構，實作流程如下：

### 1. 網格環境定義 (Environment Setup)
- 定義一個 5×5 的二維陣列來代表網格世界。
- 設定起點 `(0, 0)`、終點目標 `(4, 4)`，以及障礙物區塊 `(1,1)`, `(2,2)`, `(3,3)`。
- 定義 agent 可以採取的動作空間：上 `↑`、下 `↓`、左 `←`、右 `→`。

### 2. UI 設計與切版 (UI Design & Layout)
- 採用了具現代感的深色主題 (Dark Mode) 與毛玻璃特效 (Glassmorphism)。
- 建立左側的互動網格區塊與最佳路徑文字顯示。
- 建立右側的參數控制面板、迭代日誌追蹤與各狀態價值的表格。

### 3. 實作價值迭代算法 (Value Iteration Algorithm Core)
- 初始化 `V(s)` (價值矩陣) 為全零。
- 根據貝爾曼更新規則 (Bellman Update Rule)，對非目標、非障礙物的每一個狀態 `s`：
  $$ V(s) \leftarrow \max_a [ R(s, a, s') + \gamma \cdot V(s') ] $$
- 計算所有可能動作 `a` 帶來的新價值，並選擇最大值更新該狀態，同時記錄達到最大值的動作做為目前的政策 $\pi(s)$。
- 計算每個迭代中最大價值變化值 $\Delta$ (delta)。當 $\Delta$ 小於設定的收斂門檻 $\theta$ (theta) 時，判定算法收斂。

### 4. 互動與動畫控制 (Interactions & Animations)
- **Step (單步執行)**：允許使用者一步一步觀察 V(s) 價值的變化與暫定政策(箭頭)的指向。
- **Run (自動執行)**：利用 `requestAnimationFrame` 持續自動執行迭代，直到算法收斂，並加上動態效果。
- 追蹤並高亮顯示從起點到終點的最佳路徑 (Optimal Path)。

---

## 🚧 遇到的困難以及解決方法

### 1. 迭代過程中的動畫呈現與 UI 卡頓問題
* **困難**：如果使用一個簡單的 `while` 迴圈來執行所有的價值迭代直到收斂，會導致 JavaScript 阻擋了瀏覽器的渲染(UI Blocking)，畫面會停滯，然後瞬間跳到最後的收斂結果，無法達到「視覺化」展示的目的。
* **解決方法**：揚棄了單純的同步迴圈，改採非同步的動畫幀渲染方式。利用 `requestAnimationFrame(animateStep)` 將每一步的迭代計算切分開來。在每次迭代計算完 `V(s)` 矩陣後，立即觸發一次 `renderGrid()` 更新畫面，從而創造出平滑的計算動畫效果。

### 2. 在邊界與障礙物上的碰撞處理 (Collision Handling)
* **困難**：在計算 $s'$ 時，agent 的移動可能會跑出 5x5 的網格範圍外，或者撞上不可通行的障礙物 `(1,1)`, `(2,2)`, `(3,3)`。如果沒有正確處理，會導致程式崩潰或路徑計算錯誤。
* **解決方法**：在尋找下一個狀態 `s'` (`nr`, `nc`) 時，實作了 `isValid(r, c)` 的檢查機制。如果計算出的下一個位置超出邊界或重疊於障礙物，則讓 agent 「彈回」原地（保持 $s' = s$），並套用正常的 step penalty。確保了貝爾曼方程式在邊界狀況下的穩健性。

### 3. 無窮迴圈的路徑追蹤 (Infinite Loop in Pathfinding)
* **困難**：在某些極端的參數設定下（例如 γ 極小或 step reward 設為正數），推導出的 policy 可能會在相鄰的方塊之間來回指引，導致在繪製「最佳路徑」時產生無窮迴圈而當機。
* **解決方法**：在 `getOptimalPath()` 的函式中，引入了 `Set` 來作為 visited 狀態的記錄器。在追蹤指標時，每次走訪新的狀態都會將該座標加入 `visited`。如果發現即將走入 `visited.has(key)` 已經標記過的位置，便強制跳出迴圈，防止瀏覽器當機。

---

## 📝 專案架構
- `index.html` - 主要應用程式介面，包含了 HTML, CSS (自訂樣式) 與 JavaScript (Value Iteration 邏輯)。

> 此為深度強化學習課程 (DRL) 的實作專案之一。
