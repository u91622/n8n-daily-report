# N8N 每日廣告報告自動化工作流

這個專案利用Antigravity IDE進行製作n8n工作流，旨在節省每日整理廣告報表的時間。其自動從 Google Sheets 擷取資料，計算 CTR 與 CVR，並使用 Cerebras的API使用GPT模型生成洞察報告，最後寄送 Email。

## 功能特色
- **自動化排程**：每日早上 9:00 自動執行。
- **資料計算**：自動計算點選率 (CTR)、轉換率 (CVR) 與每次轉換成本 (CPA)。
- **AI 洞察**：使用 `gpt-oss-120b` (via Cerebras) 分析資料並提供最佳化建議。
- **Self-healing**：針對 API 請求失敗 (如 Google Sheets, AI 服務) 內建 Retry 機制，提高穩定性。

## 前置準備
你需要在 n8n 中準備好以下 Credentials：
1. **Google Sheets OAuth2 API** (用於讀取資料)
2. **Cerebras API Key** (用於 AI 分析)
3. **Gmail OAuth2 API** (用於寄信)

## 檔案說明
- `workflow.json`: n8n 工作流檔案，請直接 Import 進 n8n。
- `sample_data.csv`: 範例資料，請上傳至你的 Google Drive 並轉為 Google Sheet。

## 安裝步驟
1. **Import Workflow**:
   - 打開 n8n UI。
   - 點選右上角選單 > "Import from File"。
   - 選擇本專案中的 `workflow.json`。

2. **設定 Credentials**:
   - 點兩下 **Google Sheets** 節點，選擇你的 Google Sheets Credential。
   - 點兩下 **Cerebras AI Analysis** 節點，在 Authentication 選擇 `Header Auth`，並填入你的 API Key (Header Name: `Authorization`, Value: `Bearer <YOUR_KEY>`)。
   - 點兩下 **Send Email Report** 節點，選擇你的 Gmail Credential。

3. **設定 Google Sheet 來源**:
   - 在 Google Sheets 節點中，選擇你的試算表檔案與工作表。
   - 確保你的 Sheet 欄位包含：`Date`, `Campaign`, `Impressions`, `Clicks`, `Conversions`, `Spend` (可參考 `sample_data.csv`)。

4. **測試運行**:
   - 點選下方 "Execute Workflow" 按鈕。
   - 檢查是否收到包含 AI 分析的 Email。

5. **啟用工作流**:
   - 測試成功後，將右上角的 "Inactive" 切換為 "Active"。

## 常見問題與疑難排解

### 1. 匯入後找不到「認證 (Credential)」輸入框？
這可能是因為 n8n 匯入 JSON 時的版本差異或 UI 顯示 Bug。如果打開 Google Sheets 或 Gmail 節點卻沒看到認證選擇框：
- **解決方法 A**：點擊節點視窗上方的 **「Settings」** 分頁，檢查是否有 **「Authentication」** 選項可以開啟。
- **解決方法 B (最快解)**：直接刪除該節點，重新從右側 `+` 號搜尋並拉入一個全新的 Gmail 或 Google Sheets 節點。新節點一定會顯示認證選擇框。
- **手動填入公式**：如果手動拉新節點，請確保 Gmail 節點的 `HTML` 欄位填入公式：`{{ $json.html }}`。

### 2. Cerebras API 失敗?
檢查 API Key 是否正確（格式須為 `Bearer YOUR_KEY`），或是否超過配額。工作流已設定自動重試 3 次。

### 3. Google Sheet 讀不到?
確保您已經在 Google Sheets 節點中選擇了正確的工作表 ID。

---
*Created using Antigravity IDE*
