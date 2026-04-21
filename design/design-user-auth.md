# 文件資訊
- 狀態：Draft
- 版本：1.0.0
- 最後更新：2026-04-20
- 作者：ai-workflow-helper[bot]

# 設計目標
建立安全且可靠的使用者註冊與密碼重設系統，確保使用者能順利註冊帳戶並在忘記密碼時透過 Email 驗證機制安全恢復存取權。

# 範圍定義
## In Scope
- 註冊前端 UI 與後端 API 設計與實作。
- 密碼重設流程，包括忘記密碼請求頁面、重設密碼頁面及 Email Token 機制。
- 使用者資料庫 Schema 設計與密碼安全儲存。
- 防止惡意註冊與重設請求的限流機制。

## Out of Scope
- 第三方登入（Google/GitHub OAuth）。
- 註冊階段的 Email 驗證。
- 舊版瀏覽器相容性處理。

# 需求對應（US/FR/AC）
| 編號 | 類型 | 內容 |
|-------|-------|-------|
| US-1 | User Story | 註冊帳戶以使用部落格功能 |
| US-2 | User Story | 忘記密碼時透過 Email 收到重設連結 |
| US-3 | User Story | 密碼加密儲存以保護隱私 |
| US-4 | User Story | 限制惡意註冊與重設請求防止暴力破解 |
| FR-1 | Functional | 註冊表單包含暱稱、帳號、密碼、確認密碼 |
| FR-2 | Functional | 前端即時格式驗證，後端帳號唯一性檢查 |
| FR-3 | Functional | 密碼以 bcrypt (cost factor ≥ 10) 雜湊儲存 |
| FR-4 | Functional | 忘記密碼功能，發送 15-30 分鐘有效 Token |
| FR-5 | Functional | 重設密碼頁面驗證 Token 並更新密碼 |
| FR-6 | Functional | 註冊成功導向登入頁並提示成功 |
| FR-7 | Functional | 密碼重設 Email 寄送失敗前端提示錯誤 |
| FR-8 | Functional | 已登入使用者存取註冊頁自動跳轉 |
| NFR-1 | Non-Functional | 全站強制 HTTPS 傳輸 |
| NFR-2 | Non-Functional | API 限流機制，失敗 5 次封鎖 15 分鐘 |
| NFR-3 | Non-Functional | 輸出欄位 HTML Escape 防止 XSS |
| AC-1 | Acceptance | 格式正確註冊成功導向 /login |
| AC-2 | Acceptance | 帳號已存在回 409 錯誤並提示 |
| AC-3 | Acceptance | 密碼格式錯誤前端即時顯示錯誤 |
| AC-4 | Acceptance | 忘記密碼請求發送有效 Token |
| AC-5 | Acceptance | Token 過期或無效拒絕修改並導向錯誤頁 |
| AC-6 | Acceptance | 密碼重設成功後新密碼登入成功 |
| AC-7 | Acceptance | 同 IP 失敗超過 5 次回 429 並封鎖 |
| AC-8 | Acceptance | Email 寄送異常前端跳錯誤視窗 |

# 系統脈絡
系統包含前端註冊與密碼重設頁面，後端 API 處理註冊、密碼重設請求與 Token 驗證，使用者資料庫存放帳號資訊與加密密碼。系統依賴 SMTP 服務發送 Email，並使用 UUID 作為使用者主鍵。

# 詳細設計
## 模組拆分
- 註冊模組：處理註冊表單驗證、帳號唯一性檢查、密碼加密與使用者資料寫入。
- 密碼重設模組：管理忘記密碼請求、Token 生成與驗證、密碼更新。
- Email 發送模組：負責寄送重設密碼 Email，處理寄送失敗回報。
- 限流模組：監控 API 請求失敗次數，達到限制則封鎖。

## 流程設計
1. 使用者填寫註冊表單，前端即時驗證格式。
2. 後端檢查帳號唯一性，密碼使用 bcrypt 雜湊後存入資料庫。
3. 註冊成功後導向登入頁並提示成功。
4. 忘記密碼時，使用者輸入 Email，系統產生短效且一次性 Token，寄送重設連結。
5. 使用者點擊連結，系統驗證 Token，有效則允許重設密碼，並更新密碼後使 Token 失效。
6. 若 Token 無效或過期，導向錯誤頁。
7. API 請求失敗達限流標準時，回傳 429 並封鎖該 IP。

## 資料模型
- User：UUID 主鍵、暱稱、帳號（唯一）、密碼雜湊值、建立時間、更新時間。
- PasswordResetToken：Token 值、使用者 UUID、有效期限、是否已使用。

## 錯誤處理
- 帳號重複回 409 錯誤。
- 密碼格式錯誤前端即時提示。
- Token 無效或過期導向錯誤頁。
- Email 寄送失敗前端顯示錯誤視窗。
- 達限流封鎖回 429 錯誤。

# 安全與權限
- 密碼使用 bcrypt 雜湊，cost factor ≥ 10。
- 全站強制 HTTPS 傳輸。
- 所有輸入欄位輸出時進行 HTML Escape 防止 XSS。
- 密碼重設 Token 為一次性且短效。
- 限流機制防止暴力破解與惡意註冊。

# 效能與容量規劃
- API 限流設定為 10 分鐘內失敗 5 次封鎖 15 分鐘。
- Token 有效期設定為 15-30 分鐘。
- 預估使用者量與請求頻率，確保資料庫索引優化帳號查詢。

# 發佈與回滾策略
- 新功能先在測試環境完整驗證。
- 分階段釋出，監控錯誤率與性能。
- 發現重大問題時，立即回滾至先前穩定版本。

# 開放問題與阻塞項
- 目前缺少 API 契約文件，需補齊以利前後端對接。
- Email 發送服務異常的詳細重試策略未明確定義。

# 相關文件
- ../specs/spec-user-auth.md