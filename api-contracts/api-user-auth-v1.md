# 使用者註冊與密碼重設 API 契約

## 文件資訊
- 狀態：Draft
- 版本：1.0.0
- 最後更新：2026-04-20
- 作者：ai-workflow-helper[bot]

## API 概述
本 API 契約定義使用者註冊與密碼重設相關介面，對應功能規格 `../specs/spec-user-auth.md`，用於部落格使用者帳號管理。

## 範圍定義
### In Scope
- 註冊 API
- 忘記密碼請求 API
- 密碼重設 API

### Out of Scope
- 第三方登入
- 註冊階段 Email 驗證

## Endpoint 清單
- POST /api/register
- POST /api/password/forgot
- POST /api/password/reset

## Endpoint 詳細定義

### 1. 使用者註冊
- Method: POST
- Path: /api/register
- Content-Type: application/json
- Authentication: 無

#### Request
- Headers:
  - Content-Type: application/json
- Body:
  {
    "nickname": "string",
    "username": "string",
    "password": "string",
    "confirm_password": "string"
  }

#### Request 欄位說明
- nickname: 使用者暱稱，非必須唯一
- username: 使用者帳號，必須唯一
- password: 密碼，需符合複雜度要求
- confirm_password: 確認密碼，需與 password 相符

#### Response
- 成功 (201 Created):
  {
    "message": "註冊成功，請登入。"
  }
- 錯誤 (409 Conflict):
  {
    "error": "此帳號已被使用"
  }
- 錯誤 (400 Bad Request):
  {
    "error": "輸入格式錯誤"
  }
- 錯誤 (429 Too Many Requests):
  {
    "error": "請求過於頻繁，請稍後再試"
  }
- 錯誤 (500 Internal Server Error):
  {
    "error": "伺服器錯誤，請稍後再試"
  }

### 2. 忘記密碼請求
- Method: POST
- Path: /api/password/forgot
- Content-Type: application/json
- Authentication: 無

#### Request
- Headers:
  - Content-Type: application/json
- Body:
  {
    "email": "string"
  }

#### Request 欄位說明
- email: 使用者註冊的 Email

#### Response
- 成功 (200 OK):
  {
    "message": "重設密碼連結已發送至您的 Email。"
  }
- 錯誤 (400 Bad Request):
  {
    "error": "輸入格式錯誤"
  }
- 錯誤 (429 Too Many Requests):
  {
    "error": "請求過於頻繁，請稍後再試"
  }
- 錯誤 (500 Internal Server Error):
  {
    "error": "Email 寄送失敗，請稍後再試"
  }

### 3. 密碼重設
- Method: POST
- Path: /api/password/reset
- Content-Type: application/json
- Authentication: 無

#### Request
- Headers:
  - Content-Type: application/json
- Body:
  {
    "token": "string",
    "new_password": "string",
    "confirm_password": "string"
  }

#### Request 欄位說明
- token: 密碼重設 Token
- new_password: 新密碼，需符合複雜度要求
- confirm_password: 確認新密碼，需與 new_password 相符

#### Response
- 成功 (200 OK):
  {
    "message": "密碼重設成功，請使用新密碼登入。"
  }
- 錯誤 (400 Bad Request):
  {
    "error": "輸入格式錯誤"
  }
- 錯誤 (401 Unauthorized):
  {
    "error": "Token 無效或過期"
  }
- 錯誤 (404 Not Found):
  {
    "error": "Token 不存在或已使用"
  }
- 錯誤 (429 Too Many Requests):
  {
    "error": "請求過於頻繁，請稍後再試"
  }
- 錯誤 (500 Internal Server Error):
  {
    "error": "伺服器錯誤，請稍後再試"
  }

## 驗證規則
- username 唯一且格式正確
- password 與 confirm_password 必須相符且符合複雜度（大小寫字母與數字）
- Token 有效且未過期

## 冪等性與重試策略
- 註冊與密碼重設 API 為非冪等，重試需由前端控制避免重複提交
- 忘記密碼請求可重複發送，但受限流控制

## 版本與相容性策略
- 版本號 v1，未來變更需新增版本路徑

## 監控與稽核
- 記錄註冊與密碼重設請求日誌
- 監控失敗次數與封鎖事件

## 規格 / 測試對應
- AC-1, AC-2, AC-3：`POST /api/register`
- AC-4, AC-8：`POST /api/password/forgot`
- AC-5, AC-6, AC-7：`POST /api/password/reset`

## 開放問題與阻塞項
- Email 服務異常處理細節待補充

## 相關文件
- ../specs/spec-user-auth.md
- ../design/design-user-auth.md
- ../testing/test-flow-user-auth.md