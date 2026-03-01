# Git 問題排查與知識庫 (KM)

> **Version:** 1.0
> **Date:** 2026-03-01

## 1. 權限錯誤 (403 Permission Denied - 帳號衝突)

### 問題描述
執行 `git push` 時出現類似以下的錯誤，顯示權限被拒絕給舊的帳號（例如 `dancemi-beep`），但你當前想使用的是新帳號（例如 `M11408915`）：
```text
remote: Permission to M11408915/M11408915.github.io.git denied to dancemi-beep.
fatal: unable to access ... : The requested URL returned error: 403
```

### 原因
macOS 的「鑰匙圈存取 (Keychain)」自動記住了舊帳號的憑證。Git 預設會優先使用鑰匙圈中的舊帳號嘗試登入，導致權限不足。

### 解決方案
如果不希望刪除電腦中舊帳號的鑰匙圈紀錄，最有效的解法是**直接在遠端網址中指定正確的使用者名稱**，強制 Git 詢問該帳號的密碼。

**指令：**
```bash
# 格式：git remote set-url origin https://[正確帳號]@github.com/[帳號]/[專案].git
git remote set-url origin https://M11408915@github.com/M11408915/M11408915.github.io.git
```

---

## 2. 驗證視窗與密碼輸入 (VS Code)

### 問題描述
執行 `git push` 後，終端機似乎卡住，或在 Log 中看到 `askpass.sh`，但游標處無法輸入密碼。

### 解決方案
1. **檢查 VS Code 頂部**：VS Code 的 Git 擴充功能通常會在視窗**正上方中央**彈出一個長條形的輸入框，請在那裡輸入。
2. **強制終端機輸入**：如果沒有看到彈出視窗，可以使用環境變數強制 Git 在終端機內詢問密碼：
   ```bash
   export GIT_TERMINAL_PROMPT=1
   git push
   ```

---

## 3. 密碼認證 (Personal Access Token)

### 重要觀念
當 Git 提示輸入 `Password` 時，**不能輸入 GitHub 的登入密碼**。
必須輸入 **Personal Access Token (PAT)**。
* (Token 通常以 `ghp_` 開頭)

---

## 4. 關鍵指令速查表 (Cheat Sheet)

| 功能分類 | 指令語法 | 說明 |
| :--- | :--- | :--- |
| **檢查狀態** | `git remote -v` | 查看目前遠端倉庫的網址設定 |
| **修正權限** | `git remote set-url origin https://[ID]@github.com/...` | 修改遠端網址，強制指定使用者名稱 |
| **停用紀錄** | `git config --local credential.helper ""` | 停用此專案的密碼記憶功能 (避免用到舊帳號) |
| **除錯推送** | `export GIT_TERMINAL_PROMPT=1` | 強制在終端機顯示密碼輸入提示 (當 VS Code 沒彈窗時用) |
| **詳細日誌** | `GIT_TRACE=1 GIT_CURL_VERBOSE=1 git push origin main` | 顯示詳細連線與傳輸過程 (排查 403/401 錯誤用) |
| **執行推送** | `git push` | 上傳程式碼到 GitHub |