# 功能規格：使用者註冊與密碼重設

## 文件資訊
- **狀態**：Draft
- **版本**：1.0.0
- **最後更新**：2026-04-20
- **作者**：ai-workflow-helper[bot]

## 目標
建立部落格使用者註冊與密碼重設流程，確保使用者能安全地建立帳戶，並在遺忘密碼時透過 Email 驗證機制恢復存取權。

## 範圍定義
### In Scope
- 註冊網頁前端 UI 與後端 API 開發。
- 密碼重設流程：包含「忘記密碼」請求頁面、「重設密碼」表單頁面及 Email Token 機制。
- 使用者資料庫 Schema 設計與安全性防護。

### Out of Scope
- 第三方登入（如 Google/GitHub OAuth）。
- 註冊階段的 Email 驗證機制。
- 針對舊版瀏覽器的相容性限制。

## 使用者故事（User Stories）
- US-1：身為訪客，我希望註冊帳戶，以便能登入並使用部落格功能。
- US-2：身為使用者，我希望在忘記密碼時，能透過 Email 接收重設連結，以便恢復帳號存取。
- US-3：身為系統管理員，我希望確保使用者密碼被加密儲存，以便保護用戶隱私。
- US-4：身為系統管理員，我希望限制惡意註冊與重設請求，以便防止暴力破解攻擊。

## 功能需求（Functional Requirements）
- FR-1（Must）：提供註冊表單，包含暱稱、帳號、密碼、確認密碼欄位。
- FR-2（Must）：前端即時驗證輸入格式，後端執行帳號唯一性檢查。
- FR-3（Must）：密碼必須以 bcrypt (cost factor ≥ 10) 雜湊儲存。
- FR-4（Must）：提供「忘記密碼」功能，輸入 Email 後系統發送具備 15-30 分鐘有效期的重設 Token 連結。
- FR-5（Must）：實作「重設密碼」頁面，驗證 Token 有效性後更新密碼。
- FR-6（Must）：註冊成功後，強制導向登入頁並提示成功。
- FR-7（Must）：密碼重設 Email 寄送失敗時，前端需跳出錯誤視窗提示使用者。
- FR-8（Could）：若已登入使用者存取註冊頁，自動跳轉至首頁或儀表板。

## 非功能需求（Non-Functional Requirements）
- NFR-1：全站強制使用 HTTPS 傳輸。
- NFR-2：註冊與密碼重設 API 需實作限流機制（10 分鐘內失敗 5 次封鎖 15 分鐘）。
- NFR-3：所有輸入欄位輸出至網頁時需進行 HTML Escape 防止 XSS。

## 驗收標準（Acceptance Criteria）
- AC-1：Given 輸入格式正確，When 點擊註冊，Then 系統建立帳號並導向 `/login`。
- AC-2：Given 帳號已存在，When 送出註冊，Then API 回傳 409 錯誤，頁面提示「此帳號已被使用」。
- AC-3：Given 密碼不符合規則（大小寫及數字），When 輸入時，Then 前端即時顯示錯誤訊息。
- AC-4：Given 忘記密碼請求，When 輸入正確 Email，Then 系統發送包含有效 Token 的重設連結，且該連結具備時效性。
- AC-5：Given 密碼重設連結，When Token 過期或無效，Then 系統拒絕修改並導向錯誤頁。
- AC-6：Given 密碼重設成功，When 使用新密碼登入，Then 登入成功。
- AC-7：Given 惡意嘗試，When 同 IP 失敗超過 5 次，Then 系統回傳 429 錯誤並暫時封鎖。
- AC-8：Given Email 寄送服務異常，When 請求忘記密碼，Then 前端跳出錯誤視窗。

## 邊界情境 / 例外情況
- 密碼重設 Token 需為一次性使用，使用後即失效。
- 前端 JS 被停用時，後端仍需執行完整的驗證邏輯。
- 暱稱欄位不需唯一性限制。

## 風險與假設
- 風險 R-1（High）：密碼重設連結被攔截；緩解：連結應具備短效期且為一次性使用。
- 假設 A-1：系統具備可發送 Email 的 SMTP 服務。
- 假設 A-2：系統依賴 UUID 作為使用者主鍵。

## 相關文件
- ../api-contracts/api-user-auth-v1.md
- ../design/design-user-auth.md
- ../testing/test-flow-user-auth.md
