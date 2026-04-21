# Dify Centralized Orchestration Knowledge Base

此專案是為了 Dify 工作流（Workflow）知識庫而建立，作為集中化文件儲存與協作空間。

主要目標是把分散的資訊統一整理，降低溝通成本，並讓需求、實作、驗證可以在同一套脈絡下追蹤。

## 給 Agent 的快速導覽（請先讀這段）

此 repo 依 **角色分割 Worker**：不同角色觸發的 Agent **只負責自己那一段產出**，並把結果寫進約定目錄。  
若你不確定自己是哪一種 Agent，請依 **觸發者角色（PM / RD / QA）** 或 **系統提示中的 Worker 名稱** 對照下表。

| 觸發角色 | Worker / Agent 職責                        | 你應該產出什麼                       | 寫到哪裡                                                                      | 複製哪份模板                                                          |
| -------- | ------------------------------------------ | ------------------------------------ | ----------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| **PM**   | 規格輔助：把需求變成可審閱、可驗收的規格   | 功能規格（Spec）                     | `specs/spec-<feature-name>.md`                                                | `specs/spec-template.md`                                              |
| **RD**   | 設計與介面：在規格基礎上定義實作與對外契約 | API 契約 **與** 設計文件（兩者皆要） | `api-contracts/api-<module>-v<version>.md`、`design/design-<feature-name>.md` | `api-contracts/api-contract-template.md`、`design/design-template.md` |
| **QA**   | 測試流程：把規格與契約轉成可執行的測試設計 | 測試流程 / 測試案例設計              | `testing/test-flow-<feature-name>.md`                                         | `testing/test-flow-template.md`                                       |

**硬性規則（避免踩線）**

- PM Agent：**不要**寫 API 細節或技術設計；若缺資訊，在 Spec 標成「待 RD 補齊」並列假設。
- RD Agent：**不要**改寫 PM 的商業決策**；若 Spec 不足，在設計／契約中標註「阻塞項」並回鏈到對應 Spec 段落。
- QA Agent：**不要**發明未在 Spec / API 出現的行為；若無法對齊，標成「對齊需求」並指出缺哪份文件哪一節。

## 專案定位

本專案主要存放以下內容：

- 規格文件（Product / Functional Specs）
- API 契約（API Contract）
- 設計文件（Architecture / Technical Design）
- 技術選型基準（Tech Stack Baseline）
- 測試流程設計（Test Plan / Test Flow）

## 各目錄索引（給 Agent 快速搜尋）

- `specs/README.md`：規格文件索引與命名規範
- `api-contracts/README.md`：API 契約索引與命名規範
- `design/README.md`：設計文件與技術選型索引
- `testing/README.md`：測試流程索引與命名規範

## 常用 Prompt

- `project-reorganization-prompt.md`：整個專案重整用 Prompt（可直接複製貼到 Agent 執行）

## 重整後快速導覽

目前已建立一條完整的 `user-auth` 文件鏈，可作為後續功能擴展範本：

- Spec：`specs/spec-user-auth.md`
- API Contract：`api-contracts/api-user-auth-v1.md`
- Design：`design/design-user-auth.md`
- Test Flow：`testing/test-flow-user-auth.md`

新增功能時，請沿用同一個 `feature-name` 並補齊四份文件，避免上下游文件斷鏈。

## 各角色 Agent 的輸入／輸出檢查清單

執行任務前，先確認 **輸入是否齊全**；產出後，在文件頂部「文件資訊」填寫 **狀態、版本、最後更新**，並在文末「相關文件」互相連結。

### PM（規格輔助 Agent）

- **輸入**：產品方向、使用者情境、限制、驗收期待（可來自討論紀要或 issue）。
- **輸出**：`specs/spec-<feature-name>.md`（必填章節：目標、非目標、功能需求、驗收標準、邊界條件）。
- **完成定義**：另一角色不需再猜「要不要做」；驗收標準可被 QA 逐條對應成案例。

### RD（設計 + API 契約 Agent）

- **輸入**：對應的 `specs/spec-<feature-name>.md`（若缺，先註明阻塞再產出草案）。
- **輸出**：
  - `api-contracts/api-<module>-v<version>.md`（路徑、欄位、錯誤碼、版本策略）
  - `design/design-<feature-name>.md`（模組邊界、流程、資料模型、錯誤與發佈策略）
  - `design/tech-stack.md`（跨功能技術棧基準；如需調整選型，先更新此文件）
- **完成定義**：API 契約足以讓 QA 寫出請求／回應斷言；設計文件足以讓實作者知道模組責任與失敗處理。

### QA（測試流程 Agent）

- **輸入**：`specs/spec-<feature-name>.md` + `api-contracts/...`（設計文件 `design/...` 作為輔助，非取代契約）。
- **輸出**：`testing/test-flow-<feature-name>.md`（範圍、環境、主／例外流程、案例表、進出場條件）。
- **完成定義**：每條 P0 驗收標準至少對應一個案例 ID；錯誤類案例覆蓋契約中的主要錯誤碼。

## 用同一個功能鍵串起文件鏈（建議）

請為每個功能選一個 **穩定的 `feature-name`**（例如 `workflow-run-export`），四份文件沿用同一前綴，方便人與 Agent 搜尋：

- `specs/spec-<feature-name>.md`
- `api-contracts/api-<module>-v<version>.md`（`feature-name` 寫在契約標題或「對應功能規格」欄）
- `design/design-<feature-name>.md`
- `testing/test-flow-<feature-name>.md`

在每一份文件的「相關文件」區塊，請用 **相對路徑** 連到其他三份，維持單一脈絡。

## 建議目錄結構

可依照團隊需求調整，以下為建議起始結構：

```txt
.
├── specs/               # 功能規格、需求說明
├── api-contracts/       # API 契約、欄位與版本規範
├── design/              # 系統設計、流程圖、技術決策
│   └── tech-stack.md    # 全專案技術選型基準
└── testing/             # 測試策略、測試流程、案例設計
```

## 文件撰寫原則

- **單一事實來源（Single Source of Truth）**：同一主題只維護一份主要文件。
- **版本可追蹤**：重要異動應註明日期、作者與異動摘要。
- **可驗證**：規格與 API 契約需能直接對應到測試流程與測試案例。
- **可搜尋**：使用一致命名規則，避免模糊檔名。

## 建議命名規範

- 規格文件：`spec-<feature-name>.md`
- API 契約：`api-<service-or-module>-v<version>.md`
- 設計文件：`design-<topic>.md`
- 技術選型文件：`tech-stack.md`
- 測試流程：`test-flow-<feature-name>.md`

## 協作流程（對應 Worker 分割）

1. **PM** 觸發規格輔助 Agent → 產出並維護 `specs/spec-<feature-name>.md`。
2. **RD** 觸發設計 Agent → 讀取 Spec → 產出 `api-contracts/...` 與 `design/design-<feature-name>.md`。
3. **QA** 觸發測試流程 Agent → 讀取 Spec + API 契約（與必要時的 Design）→ 產出 `testing/test-flow-<feature-name>.md`。
4. 任一環節若發現上游不足：**在上游文件標註阻塞／待補段落**，不要只在下游口頭描述。
5. Spec、契約或驗收標準變更時：**同步更新**下游文件並更新版本／最後更新日。

## 後續可擴充項目

- 文件狀態標記（Draft / Review / Approved）與 Review 流程
- 自動化檢查（例如檔名規範、必要章節檢查）
- 與 Dify 實際流程或部署環境的對應指引（各 Worker 的觸發節點與變數命名）

---
