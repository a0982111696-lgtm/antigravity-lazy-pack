# Anti-Gravity 懶人包環境建置日誌

## 🛠️ 安裝元件詳細列表與相容性檢查

- **#01 NotebookLM**：成功
  - 工具：`notebooklm-mcp-cli` v0.6.11 已安裝完成。
  - 防錯：已在 PATH 中登錄，若遇到路徑問題可改用 `python -m notebooklm_mcp_cli`。
  - 診斷：`nlm doctor` 已成功運作。
- **#02 連接 GitHub**：成功
  - 工具：`git` (v2.54.0) 與 `gh` (v2.92.0) 已安裝完成。
  - 防錯：建議執行 `gh auth login` 透過 Web 完成安全認證。
- **#03 Obsidian**：成功
  - 工具：`@bitbonsai/mcpvault` 已安裝完成。
  - 位置：`C:\Users\木木\AppData\Roaming\npm\mcpvault.cmd`
- **#05 Firebase**：成功
  - 工具：`firebase-tools` 已安裝完成。
  - 位置：`C:\Users\木木\AppData\Roaming\npm\firebase.cmd`
- **#06 瀏覽器控制**：成功
  - 工具：`open-computer-use` 已全域安裝完成。
  - 位置：`C:\Users\木木\AppData\Roaming\npm\open-computer-use.cmd`

---

## 💡 Windows 相容性總結與優勢

1. **環境變數 CP950 解碼修正**：
   - 現象：繁體中文系統執行特殊字元（如 ✓ ✗ 等）會報 CP950 解碼錯誤。
   - 解法：在指令呼叫前設定環境變數 `$env:PYTHONIOENCODING = "utf-8"`。
2. **GITHUB_TOKEN 無效覆蓋修正**：
   - 現象：環境傳入的無效 GITHUB_TOKEN 會覆蓋本地正常的 GitHub 登入認證。
   - 解法：執行 `gh` 指令前，清空當前連線的 `$env:GITHUB_TOKEN`。
3. **免 API Key 生圖與原生 Skills 支援**：
   - 完美相容 Anti-Gravity 2 原生生態！
