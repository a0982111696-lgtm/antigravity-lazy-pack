# Anti-Gravity 懶人包 #09：服務連接與工作流程設定

> 版本：v2.0 (專屬防錯版)
> 更新日期：2026-05-24
> 語系偏好：繁體中文（Taiwan）

這份懶人包的目標，是讓 Anti-Gravity 使用者能安全、快速且無痛地連接 NotebookLM、Firebase、GitHub、Obsidian，並建立極致高效的「開工 / 收工 / 新專案初始化」AI 協作工作流程。

本文件只放可公開教學的設定流程，**絕不存放**任何個人 NotebookLM 清單、筆記本 ID、研究報告、生成圖片、帳號 Token 或測試專案。

---

## 零、 Windows `winget` 一鍵快速安裝先備工具 (全新新增)

在 Windows 10 / 11 系統中，您可以利用系統內建的包管理器 `winget` 在 10 秒內一鍵安裝所有缺少的環境工具。

### 🚀 一鍵自動安裝
請以**系統管理員身分**開啟 PowerShell，然後複製並執行以下指令：

```powershell
# 自動安裝 Git、GitHub CLI、Node.js 與 uv 包管理器
winget install --id Git.Git -e --silent; winget install --id GitHub.cli -e --silent; winget install --id OpenJS.NodeJS -e --silent; winget install --id astral-sh.uv -e --silent
```

> [!IMPORTANT]
> 安裝完成後，**必須重啟所有的 PowerShell 視窗**以套用全新的環境變數設定。

### 🔍 環境快速檢查
重啟 PowerShell 後，執行以下指令驗證安裝：

```powershell
git --version
gh --version
node --version
npm.cmd --version
uv --version
python --version
```

---

## 一、連接 NotebookLM

### 重點原則
NotebookLM 登入應走瀏覽器 OAuth 授權。不要複製 Cookie、Token，也不要把 NotebookLM 匯出的 `notebooks.json` 或筆記本 ID 清單放進公開 Repo 中。

### 1. 安裝 NotebookLM MCP CLI

**推薦方案 (使用 `uv`，速度最快)：**
```powershell
uv tool install notebooklm-mcp-cli
nlm --version
```

**備用方案 (無 `uv` 時，使用 `pip`)：**
```powershell
python -m pip install notebooklm-mcp-cli
nlm --version
```

> [!WARNING]
> **Windows 環境防錯（PATH 未設定問題）**：
> 如果安裝後執行 `nlm` 顯示「無法辨識此指令」，這是因為 Python 的 Scripts 目錄未加入您的 Windows PATH。
> * **解決方案 1**：改用 Python 直接呼叫模組：
>   ```powershell
>   python -m notebooklm_mcp_cli --version
>   ```
> * **解決方案 2**：手動將 Python Scripts 目錄加入 PATH。路徑通常位於：
>   `%USERPROFILE%\AppData\Local\Programs\Python\Python313\Scripts` (請依據您的 Python 版本調整)。

### 2. 重新登入 Google 帳號

若曾經登入錯帳號，先登出再重新 OAuth：

```powershell
nlm logout
nlm login
```

`nlm login` 會開啟瀏覽器，請在瀏覽器選擇正確的 Google 帳號完成授權。

驗證連線狀態：

```powershell
nlm doctor
nlm list notebooks
```

> [!TIP]
> 如果 Windows 顯示 **CP950 / Unicode 編碼錯誤**，可在 PowerShell 視窗先設定環境變數再重新執行：
> ```powershell
> $env:PYTHONIOENCODING = "utf-8"
> nlm doctor
> ```

### 3. 註冊 MCP

在 Anti-Gravity 的 MCP 設定檔（通常為 `antigravity.json` 或 UI 設定介面）中，加入以下設定：

```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": ["nlm", "mcp"],
      "enabled": true
    }
  }
}
```

*若使用 Python 備用呼叫路徑，設定如下：*
```json
{
  "mcp": {
    "notebooklm": {
      "type": "local",
      "command": ["python", "-m", "notebooklm_mcp_cli", "mcp"],
      "enabled": true
    }
  }
}
```

完成後重啟 Anti-Gravity，請它列出 NotebookLM 筆記本。**只回報是否成功，不要把完整清單 commit 到 repo**。

---

## 二、連接 GitHub

### 1. 登入 GitHub CLI

```powershell
gh auth status
gh auth login --web --git-protocol https
gh auth status
```

若登入流程卡住，請在可互動的 PowerShell 視窗完成瀏覽器授權，再回來驗證。

### 2. 設定 Git 使用者

```powershell
git config --global user.name "你的名字"
git config --global user.email "your-email@example.com"
```

若不想公開個人真實信箱，可至 GitHub 設定中啟用 `Keep my email addresses private`，並在此處使用 GitHub 提供之 `no-reply` email。

### 3. 安全規則

- GitHub 與 GitHub Copilot 是不同服務；本流程只需要 GitHub 帳號、Git、GitHub CLI。
- **絕對不要**把 GitHub Personal Access Token (PAT) 寫進 Markdown、AGENTS、ANTIGRAVITY、Obsidian 筆記或任何 Repo 中。
- commit 前先檢查 `git diff`，避免無差別提交。

---

## 三、連接 Firebase

### 1. 安裝與登入

**方案 A (推薦，若有 Node.js 環境)：**
Windows 建議使用 `npx.cmd`，避免 PowerShell 執行原則阻擋 `.ps1` 腳本：
```powershell
npx.cmd -y firebase-tools@latest --version
npx.cmd -y firebase-tools@latest login
npx.cmd -y firebase-tools@latest projects:list
```

**方案 B (免 Node.js 替代方案)：**
若使用者不想安裝 Node.js，可至 Firebase 官網下載適用於 Windows 的 `firebase-tools-instant-win.exe` 獨立二進位檔，將其重新命名為 `firebase.exe` 並放進系統 PATH 中。

### 2. 註冊 Firebase MCP

在 Anti-Gravity 的 MCP 設定檔中，加入以下設定概念：

```json
{
  "mcp": {
    "firebase": {
      "type": "local",
      "command": ["npx.cmd", "-y", "firebase-tools@latest", "mcp"],
      "enabled": true
    }
  }
}
```

完成後重啟 Anti-Gravity，測試列出 Firebase 專案與 Firestore 集合。

### 3. 安全規則

- Firebase 前端 Config (例如 `apiKey`, `authDomain`) 可以公開，但 **Firebase Admin SDK 憑證金鑰 (JSON 檔案) 絕對不可公開**。
- `.firebaserc` 若含私人專案 ID，公開前請確認是否適合。
- 專案涉及學生資料時，**只存班級代號與座號**，不存真名，保護學生隱私。

---

## 四、連接 Obsidian

### 1. 找到 Vault 路徑

請先確認 Obsidian Vault 的實體路徑。常見路徑：

```text
C:\Users\<你>\OneDrive\文件\Secondbrain
C:\Users\<你>\Documents\<vault 名稱>
G:\我的雲端硬碟\<vault 名稱>
```

確認條件：
- 資料夾確實存在。
- 資料夾根目錄下包含隱藏的 `.obsidian` 資料夾。
- 這是您平常真正使用的工作筆記本。

### 2. 安裝與註冊 MCPVault

透過全域 NPM 安裝 MCPVault：
```powershell
npm.cmd install -g @bitbonsai/mcpvault
where.exe mcpvault
```

在 Anti-Gravity 的 MCP 設定中配置路徑（請替換為您電腦上的實際路徑）：

```json
{
  "mcp": {
    "obsidian": {
      "type": "local",
      "command": [
        "C:\\Users\\<你>\\AppData\\Roaming\\npm\\mcpvault.cmd",
        "C:\\Users\\<你>\\OneDrive\\文件\\Secondbrain"
      ],
      "enabled": true
    }
  }
}
```

完成後重啟 Anti-Gravity，測試讀取 Vault 根目錄，並建立一篇測試筆記確認讀寫正常。

---

## 五、生圖與資產管理

如果 Anti-Gravity 內建生圖工具，可直接用自然語言產生圖片，不需要將 OpenAI API key 或 Midjourney token 寫進專案。

**建議提示格式 (Prompt Template)：**
```text
生成一張圖片：
- 用途：[例如：首頁大圖、文章插圖]
- 尺寸比例：[例如：16:9、1:1]
- 主題：[精確描述主體]
- 畫面內容：[細節場景、元素配備]
- 風格：[例如：現代簡約、玻璃擬態、極簡暗黑]
- 色彩：[如：深藍 HSL(220, 80%, 10%) 到 紫色 HSL(280, 80%, 15%) 的漸層]
- 限制：[不出現設備框、不出現文字]
- 輸出位置：[e.g. assets/hero_image.png]
```

---

## 六、開工 / 收工 / 新專案初始化 SOP

Anti-Gravity 可以讀取專案根目錄的 `ANTIGRAVITY.md` 作為 AI 工作規則入口。
* `ANTIGRAVITY.md`：記錄**固定規則、路徑、專案邊界與 Do / Don't**。
* **Obsidian 專案駕駛艙**：記錄**每日流水帳、進度、踩坑、臨時筆記**。

### 🌅 1. 開工流程 (Daily Warm-up)
當您對 AI 說「開工」時，AI 會自動執行以下流程：
1. **讀取規則**：讀取專案根目錄的 `ANTIGRAVITY.md`。
2. **讀取日誌**：透過 Obsidian MCP 讀取專案駕駛艙最近日誌。
3. **檢查狀態**：執行 `git status` 與最近 3 筆 Commit 檢查。
4. **主動回報**：向使用者簡要回報「目前專案進度、代辦清單、以及今日建議執行的首要任務」。

### 🌌 2. 收工流程 (Daily Cool-down)
當您對 AI 說「收工」時，AI 會自動執行以下流程：
1. **安全掃描**：掃描專案是否殘留任何 API Key、Token、Firebase 憑證或個人隱私資料。
2. **更新日誌**：將今日完成事項、未完成的 Next Steps、遇到的困難（踩坑）寫入 Obsidian 專案駕駛艙。
3. **更新規則**：僅在固定規則改變時才更新 `ANTIGRAVITY.md`。
4. **Git 清理與提交**：
   - 執行 `git status` 與 `git diff`，向您展示變更。
   - **精準 Stage**：只 stage 本次相關檔案，**絕不**使用無差別 `git add .`。
   - 產生標準 Commit Message，由您確認後提交並 Push。

### 🚀 3. 新專案初始化流程
當您要求「初始化新專案」時，AI 會主動盤點並建立：
1. `ANTIGRAVITY.md` (寫入專案入口資訊與工作規則)。
2. `README.md` (基礎專案介紹)。
3. `.gitignore` (自動避開本機暫存、API Key、生圖暫存與 Obsidian 私人設定)。
4. 初始化 Git 本地 Repo (並引導關聯 GitHub 遠端)。

---

## 📄 建議的 ANTIGRAVITY.md 範本

```markdown
# <專案名稱> - ANTIGRAVITY.md

## 專案入口
- 專案名稱：
- 專案用途：
- 主要工作目錄：
- GitHub Repo：
- 預設 Branch：main

## Obsidian 對應筆記
- Obsidian Vault 路徑：
- 專案駕駛艙筆記檔名：

## 工作規則
- 回應使用繁體中文（Taiwan）。
- 涉及任何檔案操作時，請務必回報完整產出位置。
- 使用 Windows PowerShell 語法執行指令。
- 開工時：讀取本檔、讀取 Obsidian 專案駕駛艙、檢查 Git 狀態。
- 收工時：更新 Obsidian 駕駛艙、檢查 Diff，只提交與任務高度相關的檔案。

## 安全紅線 (DON'T)
- 絕對不要 Commit 任何 API Key、Token、密碼或 Firebase Admin 私鑰。
- 絕對不要 Commit 學生真名，一律轉化為「班級代號 + 座號」。
- 避免自動 pull/push，任何提交動作均須由使用者確認後執行。
```

---

## 💡 常見問題與排錯指南

| 問題現象 | 根本原因 | 快速解決方案 |
| :--- | :--- | :--- |
| **`nlm` 顯示無法辨識此指令** | Python Scripts 未加入 PATH。 | 改用 `python -m notebooklm_mcp_cli` 代替，或手動加入系統 PATH。 |
| **NotebookLM 顯示編碼錯誤** | Windows 終端機預設編碼相容問題。 | 執行 `$env:PYTHONIOENCODING = "utf-8"` 後重試。 |
| **Firebase 登入卡住** | 瀏覽器 OAuth 彈出失敗。 | 手動在外部開啟實體 PowerShell 執行 `npx.cmd firebase login`，完成後再讓 AI 驗證。 |
| **`npx.cmd` 顯示無法辨識** | 電腦未安裝 Node.js。 | 請先使用 `winget install --id OpenJS.NodeJS -e` 安裝。 |
| **收工時自動 Commit 了無關檔案** | 使用了自動化 `git add .`。 | AI 會優先執行 `git status`，並由您手動確認應 stage 的檔案路徑，確保 Git 歷史乾淨。 |

---

## 📈 更新紀錄

| 日期 | 版本 | 更新內容 |
| :--- | :--- | :--- |
| **2026-05-24** | **v2.0** | **專屬防錯版：補齊 Obsidian 與工作 SOP，新增 `winget` 一鍵環境建置指令、NotebookLM PATH 排錯、Firebase Standalone 方案。** |
| 2026-05-23 | v1.1 | 移除 NotebookLM 個人資料定位，修正 OAuth、Obsidian MCP 與 Git 安全流程。 |
| 2026-05-22 | v1.0 | 初版上線。 |
