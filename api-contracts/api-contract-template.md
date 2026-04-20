# API 契約模板（API Contract Template）

## 文件資訊

- API 名稱：
- 所屬服務 / 模組：
- 版本：v1
- 狀態：Draft / Review / Approved
- 作者：
- 建立日期：
- 最後更新：

## API 概述

- 目的：
- 對應功能規格：
- 使用場景：

## Endpoint 定義

- Method：
- Path：
- Content-Type：
- Authentication：

## Request

### Headers

| Key | Required | Example | 說明 |
| --- | --- | --- | --- |
| Authorization | Yes | Bearer xxx | 存取憑證 |

### Path Parameters

| Name | Type | Required | Example | 說明 |
| --- | --- | --- | --- | --- |
| id | string | Yes | wf_123 | 資源識別碼 |

### Query Parameters

| Name | Type | Required | Default | Example | 說明 |
| --- | --- | --- | --- | --- | --- |
| page | integer | No | 1 | 1 | 分頁頁碼 |

### Request Body（JSON）

```json
{
  "fieldA": "string",
  "fieldB": 123
}
```

### Request 欄位說明

| Field | Type | Required | Constraints | 說明 |
| --- | --- | --- | --- | --- |
| fieldA | string | Yes | max: 100 | 欄位 A 說明 |
| fieldB | integer | No | min: 0 | 欄位 B 說明 |

## Response

### 成功回應

- Status Code：200 OK

```json
{
  "code": "OK",
  "message": "success",
  "data": {}
}
```

### 錯誤回應

| Status Code | Error Code | 說明 |
| --- | --- | --- |
| 400 | INVALID_REQUEST | 參數驗證失敗 |
| 401 | UNAUTHORIZED | 未授權 |
| 500 | INTERNAL_ERROR | 系統錯誤 |

```json
{
  "code": "INVALID_REQUEST",
  "message": "fieldA is required"
}
```

## 驗證規則

- 規則 1：
- 規則 2：
- 規則 3：

## 冪等性與重試策略

- 是否冪等：
- 冪等鍵：
- 重試建議：

## 版本與相容性策略

- 破壞性變更定義：
- 升版規則：
- 相容期規劃：

## 監控與稽核

- 需要紀錄的欄位：
- 追蹤 ID（Trace ID）：
- 稽核需求：

## 相關文件

- 功能規格：
- 設計文件：
- 測試流程：
