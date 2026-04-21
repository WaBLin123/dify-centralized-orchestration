# 專案重整 Prompt

```text
你是資深 Tech Writer + 系統分析師 + 文件治理工程師。
請「重整整個專案」以建立一致、可追蹤、可驗證的知識庫結構，並直接在 repo 內完成必要調整。

【專案背景】
- 這是一個以文件為核心的專案，不是純程式碼專案。
- 主要目錄：`specs/`、`api-contracts/`、`design/`、`testing/`
- 核心目標：讓 PM / RD / QA 三種角色能用同一條 feature 脈絡協作。
- 命名核心：同一個功能要用一致的 `feature-name` 串起四類文件。

【你的任務】
1) 全面盤點目前文件
- 列出所有現有文件與其目的。
- 找出命名不一致、內容重複、章節缺漏、互相連結缺失、上下游不對齊（Spec/API/Design/Test）問題。

2) 定義並執行重整規則
- 統一命名規範：
  - `specs/spec-<feature-name>.md`
  - `api-contracts/api-<service-or-module>-v<version>.md`
  - `design/design-<feature-name>.md`
  - `testing/test-flow-<feature-name>.md`
- 保留單一事實來源（Single Source of Truth），避免跨檔重複定義。
- 每份文件都要補齊「文件資訊」（狀態/版本/最後更新/作者）。
- 每份文件都要補齊「相關文件」相對路徑連結。
- 若發現不合理內容，優先「最小必要修正」，不要任意擴寫超出原需求。

3) 檢查跨文件一致性
- Spec 的 FR/AC 必須可被 API 契約與測試流程對應。
- API 錯誤碼、欄位命名、流程敘述需與 Design/Test 一致。
- Test case 必須可回鏈到 AC 與主要錯誤碼。

4) 更新索引與導覽
- 更新各目錄 README（`specs/README.md`、`api-contracts/README.md`、`design/README.md`、`testing/README.md`），確保能快速找到文件。
- 若需要，補上「重整後導覽」段落於根目錄 README。

【執行限制】
- 不要刪除有價值內容；必要時以「合併/搬移/重命名」替代硬刪。
- 不要改變需求本質（商業決策）與 API 行為，除非明確標記為「待確認」。
- 避免過度重寫：優先結構與一致性修正。
- 使用相對路徑連結，不使用絕對路徑。

【輸出格式（請嚴格遵守）】
A. 先輸出「重整計畫摘要」（條列 5-10 點）
B. 再輸出「實際變更清單」
  - 新增檔案
  - 重新命名檔案
  - 修改檔案
  - （若有）刪除檔案與理由
C. 最後輸出「一致性驗證報告」
  - Feature chain 是否完整（Spec/API/Design/Test）
  - AC 對應測試覆蓋是否完整
  - 尚未解決的阻塞項（明確列出）

【品質標準】
- 命名一致
- 導覽清楚
- 可追蹤（版本/連結/對應關係）
- 可驗證（AC ↔ Test、Spec ↔ API）
- 可維護（後續新增 feature 能依同規則擴展）

現在開始執行，直接完成重整並回報結果。
```
