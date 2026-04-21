# API Contracts 目錄索引

本目錄存放 API 契約文件，定義介面、欄位、錯誤碼與版本策略，作為 RD 與 QA 對齊依據。

## 目錄內容

- `api-contract-template.md`：API 契約模板。
- `api-<service-or-module>-v<version>.md`：模組或服務的契約文件。

## 建議命名

- 檔名格式：`api-<service-or-module>-v<version>.md`
- 範例：`api-workflow-v1.md`

## Agent 快速搜尋建議

- 先讀模板：`api-contract-template.md`
- 依模組與版本鎖定目標文件，例如 `api-xxx-v1.md`
- 測試或串接前，先確認錯誤碼與版本策略章節是否完整。
