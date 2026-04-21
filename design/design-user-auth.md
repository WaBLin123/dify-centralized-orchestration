# 使用者註冊與密碼重設設計文件

## 文件資訊
- 狀態：Draft
- 版本：1.0.0
- 最後更新：2026-04-20
- 作者：ai-workflow-helper[bot]

## 設計目標
建立安全且易用的使用者註冊與密碼重設系統，確保用戶資料安全與流程順暢，防止惡意攻擊。

## 範圍定義
### In Scope
- 使用者註冊流程與 API
- 密碼重設流程與 API，包括 Email Token 機制
- 使用者資料庫 Schema 設計與安全性防護
- 限流機制以防止暴力破解

### Out of Scope
- 第三方登入（Google/GitHub OAuth）
- 註冊階段 Email 驗證
- 舊版瀏覽器相容性

## 需求對應（US/FR/AC）
- US-1 至 US-4
- FR-1 至 FR-8
- AC-1 至 AC-8

## 系統脈絡
使用者透過前端註冊頁面提交資料，後端驗證並儲存，密碼以 bcrypt 雜湊。忘記密碼時，系統發送含 Token 的 Email，使用者點擊後可重設密碼。

## 詳細設計
### 模組拆分
- 註冊模組：處理註冊請求、格式驗證、帳號唯一性檢查、密碼加密
- 密碼重設模組：處理忘記密碼請求、Token 產生與驗證、密碼更新
- Email 服務模組：負責發送重設密碼 Email
- 限流模組：監控失敗次數，封鎖惡意 IP

### 流程設計
1. 註冊流程：
   - 前端格式驗證
   - 後端帳號唯一性檢查
   - 密碼 bcrypt 雜湊
   - 建立使用者資料
   - 導向登入頁
2. 忘記密碼流程：
   - 輸入 Email
   - 後端產生 Token，存入資料庫並設定有效期
   - 發送 Email
3. 重設密碼流程：
   - 驗證 Token
   - 更新密碼（bcrypt 雜湊）
   - Token 一次性失效

### 資料模型
- User (id: UUID, nickname, username, password_hash, created_at, updated_at)
- PasswordResetToken (token, user_id, expires_at, used_flag)

### 錯誤處理
- 409 帳號已存在
- 400 輸入格式錯誤
- 401 Token 無效或過期
- 404 Token 不存在或已使用
- 429 限流封鎖
- 500 伺服器錯誤

## 安全與權限
- 密碼使用 bcrypt 雜湊，cost factor ≥ 10
- Token 一次性使用且有效期 15-30 分鐘
- 全站強制 HTTPS
- 限流防止暴力破解
- 輸入輸出進行 HTML Escape 防止 XSS

## 效能與容量規劃
- 限流機制避免系統過載
- Token 資料庫索引優化查詢效率

## 發佈與回滾策略
- 分階段部署，先內部測試再全量上線
- 發現問題可回滾至前一版本

## 開放問題與阻塞項
- Email 服務異常時的補救措施需明確定義
- Token 失效通知流程細節待確認

## 相關文件
- ../specs/spec-user-auth.md
- ../api-contracts/api-user-auth-v1.md
- ../testing/test-flow-user-auth.md
- ./tech-stack.md
