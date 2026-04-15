# API接口文档 v1.0

> 基于业务流程文档设计，符合Restful规范，易于实现

---

## 1. 概述

### 1.1 基础信息

| 项目 | 值 |
|------|-----|
| 基础URL | `/api/v1` |
| 请求格式 | `Content-Type: application/json` |
| 认证方式 | JWT Bearer Token |
| 字符编码 | UTF-8 |

### 1.2 认证说明

除公开接口（注册、登录）外，所有接口需要在请求头中携带Token：

```
Authorization: Bearer <token>
```

### 1.3 响应格式

所有接口统一响应格式：

**成功响应：**
```json
{
  "status": 200,
  "data": { ... },
  "message": "ok"
}
```

**错误响应：**
```json
{
  "status": 400,
  "data": null,
  "message": "错误描述"
}
```

### 1.4 HTTP状态码

| 状态码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 400 | 请求参数错误 |
| 401 | 未授权（未登录或Token无效） |
| 403 | 禁止访问（无权限） |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

### 1.5 业务错误码

| 错误码 | 说明 |
|--------|------|
| 1001 | 用户名已存在 |
| 1002 | 用户名或密码错误 |
| 1003 | Token已过期 |
| 2001 | 会话不存在 |
| 2002 | 会话不属于该用户 |
| 3001 | 事件格式错误 |
| 3002 | 事件类型不支持 |

---

## 2. 用户模块

### 2.1 用户注册

**接口**: `POST /api/v1/auth/register`

**描述**: 创建新用户账号

**请求参数**:
```json
{
  "username": "zhangsan",
  "password": "123456",
  "confirmPassword": "123456"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| username | string | 是 | 用户名，3-32字符 |
| password | string | 是 | 密码，6-32字符 |
| confirmPassword | string | 是 | 确认密码，需与密码一致 |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "userId": 1,
    "username": "zhangsan"
  },
  "message": "注册成功"
}
```

**错误响应**:
- 1001: 用户名已存在
- 400: 密码不一致

---

### 2.2 用户登录

**接口**: `POST /api/v1/auth/login`

**描述**: 用户登录，获取Token

**请求参数**:
```json
{
  "username": "zhangsan",
  "password": "123456"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| username | string | 是 | 用户名 |
| password | string | 是 | 密码 |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "userId": 1,
    "username": "zhangsan"
  },
  "message": "登录成功"
}
```

**错误响应**:
- 1002: 用户名或密码错误

---

### 2.3 获取用户信息

**接口**: `GET /api/v1/auth/profile`

**描述**: 获取当前登录用户信息

**请求头**: 需要认证

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "userId": 1,
    "username": "zhangsan",
    "createdAt": "2026-04-09T10:00:00Z"
  },
  "message": "ok"
}
```

---

## 3. 对话模块

### 3.1 创建会话

**接口**: `POST /api/v1/chat/sessions`

**描述**: 创建新的对话会话

**请求头**: 需要认证

**请求参数**:
```json
{
  "title": "如何实现单例模式"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| title | string | 否 | 会话标题，默认"新对话" |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "sessionId": "sess_abc123",
    "title": "如何实现单例模式",
    "createdAt": "2026-04-09T10:00:00Z",
    "status": "active"
  },
  "message": "创建成功"
}
```

---

### 3.2 获取会话列表

**接口**: `GET /api/v1/chat/sessions`

**描述**: 获取当前用户的所有会话列表

**请求头**: 需要认证

**查询参数**:
| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| page | int | 否 | 页码，默认1 |
| pageSize | int | 否 | 每页数量，默认20 |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "sessions": [
      {
        "sessionId": "sess_abc123",
        "title": "如何实现单例模式",
        "createdAt": "2026-04-09T10:00:00Z",
        "updatedAt": "2026-04-09T10:05:00Z",
        "messageCount": 5
      }
    ],
    "total": 10,
    "page": 1,
    "pageSize": 20
  },
  "message": "ok"
}
```

---

### 3.3 获取会话详情

**接口**: `GET /api/v1/chat/sessions/:sessionId`

**描述**: 获取指定会话的详细信息

**请求头**: 需要认证

**路径参数**:
| 字段 | 类型 | 说明 |
|------|------|------|
| sessionId | string | 会话ID |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "sessionId": "sess_abc123",
    "title": "如何实现单例模式",
    "createdAt": "2026-04-09T10:00:00Z",
    "updatedAt": "2026-04-09T10:05:00Z",
    "status": "active"
  },
  "message": "ok"
}
```

**错误响应**:
- 2001: 会话不存在
- 2002: 会话不属于该用户

---

### 3.4 发送消息

**接口**: `POST /api/v1/chat/sessions/:sessionId/messages`

**描述**: 向指定会话发送用户消息，系统自动调用LLM并返回响应

**请求头**: 需要认证

**路径参数**:
| 字段 | 类型 | 说明 |
|------|------|------|
| sessionId | string | 会话ID |

**请求参数**:
```json
{
  "content": "如何用Java实现单例模式？"
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| content | string | 是 | 用户消息内容 |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "userMessage": {
      "id": 1001,
      "role": "user",
      "content": "如何用Java实现单例模式？",
      "createdAt": "2026-04-09T10:00:00Z"
    },
    "aiMessage": {
      "id": 1002,
      "role": "ai",
      "content": "单例模式是Java中常用的设计模式之一...",
      "createdAt": "2026-04-09T10:00:02Z"
    }
  },
  "message": "ok"
}
```

**错误响应**:
- 2001: 会话不存在
- 2002: 会话不属于该用户

---

### 3.5 获取消息历史

**接口**: `GET /api/v1/chat/sessions/:sessionId/messages`

**描述**: 获取指定会话的所有消息历史

**请求头**: 需要认证

**路径参数**:
| 字段 | 类型 | 说明 |
|------|------|------|
| sessionId | string | 会话ID |

**查询参数**:
| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| page | int | 否 | 页码，默认1 |
| pageSize | int | 否 | 每页数量，默认50 |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "messages": [
      {
        "id": 1001,
        "role": "user",
        "content": "如何用Java实现单例模式？",
        "createdAt": "2026-04-09T10:00:00Z"
      },
      {
        "id": 1002,
        "role": "ai",
        "content": "单例模式是Java中常用的设计模式之一...",
        "createdAt": "2026-04-09T10:00:02Z"
      }
    ],
    "total": 5,
    "page": 1,
    "pageSize": 50
  },
  "message": "ok"
}
```

---

### 3.6 清空对话

**接口**: `DELETE /api/v1/chat/sessions/:sessionId/messages`

**描述**: 清空指定会话的所有消息

**请求头**: 需要认证

**路径参数**:
| 字段 | 类型 | 说明 |
|------|------|------|
| sessionId | string | 会话ID |

**成功响应** (200):
```json
{
  "status": 200,
  "data": null,
  "message": "清空成功"
}
```

**错误响应**:
- 2001: 会话不存在
- 2002: 会话不属于该用户

---

### 3.7 重试AI回答

**接口**: `POST /api/v1/chat/sessions/:sessionId/messages/:messageId/retry`

**描述**: 重新生成AI响应的替代回答

**请求头**: 需要认证

**路径参数**:
| 字段 | 类型 | 说明 |
|------|------|------|
| sessionId | string | 会话ID |
| messageId | number | AI消息ID |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "originalMessage": {
      "id": 1002,
      "role": "ai",
      "content": "单例模式是Java中..."
    },
    "newMessage": {
      "id": 1003,
      "role": "ai",
      "content": "Java单例模式通常有以下几种实现方式..."
    }
  },
  "message": "ok"
}
```

**错误响应**:
- 2001: 会话不存在
- 2002: 会话不属于该用户
- 400: 消息不存在或不是AI消息

---

## 4. 行为数据模块

### 4.1 上报行为事件

**接口**: `POST /api/v1/track`

**描述**: 前端上报用户行为事件，支持批量上报

**请求头**: 需要认证

**请求参数**:
```json
{
  "sessionId": "sess_abc123",
  "events": [
    {
      "eventId": "evt_001",
      "eventType": "LLM_QUERY",
      "timestamp": 1712644800000,
      "payload": {
        "prompt": "如何用Java实现单例模式？",
        "uiElement": "chat_input"
      },
      "context": {
        "device": "MacBook",
        "os": "macOS",
        "browser": "Chrome"
      }
    },
    {
      "eventId": "evt_002",
      "eventType": "LLM_RESPONSE",
      "timestamp": 1712644802000,
      "payload": {
        "responseText": "单例模式是...",
        "generationTimeMs": 2000,
        "ttftMs": 500
      }
    }
  ]
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| sessionId | string | 是 | 会话ID |
| events | array | 是 | 事件数组，最多100条 |
| events[].eventId | string | 是 | 事件唯一ID |
| events[].eventType | string | 是 | 事件类型 |
| events[].timestamp | int | 是 | 事件时间戳（毫秒） |
| events[].payload | object | 否 | 事件数据 |
| events[].context | object | 否 | 上下文信息 |

**事件类型说明**:

| 事件类型 | 说明 | payload示例 |
|----------|------|-------------|
| PAGE_LOAD | 页面加载 | `{ referrer, device, os }` |
| LLM_QUERY | 用户提问 | `{ prompt, uiElement }` |
| LLM_RESPONSE | AI响应 | `{ responseText, generationTimeMs, ttftMs }` |
| THUMBS_UP | 点赞 | `{ turnId }` |
| THUMBS_DOWN | 点踩 | `{ turnId, reason }` |
| COPY_RESULT | 复制结果 | `{ turnId }` |
| ERROR | 错误发生 | `{ errorType, errorMessage, turnId }` |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "receivedCount": 2
  },
  "message": "ok"
}
```

**错误响应**:
- 3001: 事件格式错误
- 3002: 事件类型不支持

---

## 5. 分析模块（管理后台）

### 5.1 概览统计

**接口**: `GET /api/v1/insight/overview`

**描述**: 获取整体统计数据

**请求头**: 需要认证（管理员）

**查询参数**:
| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| startDate | string | 否 | 开始日期 YYYY-MM-DD |
| endDate | string | 否 | 结束日期 YYYY-MM-DD |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "totalUsers": 100,
    "totalSessions": 500,
    "totalMessages": 2500,
    "avgTurnDepth": 5.3,
    "todayUsers": 20,
    "todaySessions": 50,
    "dailyStats": [
      {
        "date": "2026-04-09",
        "users": 20,
        "sessions": 50,
        "avgDepth": 5.3
      },
      {
        "date": "2026-04-08",
        "users": 18,
        "sessions": 45,
        "avgDepth": 4.8
      }
    ]
  },
  "message": "ok"
}
```

---

### 5.2 旅程图

**接口**: `GET /api/v1/insight/journey/:sessionId`

**描述**: 获取指定会话的用户行为旅程图

**请求头**: 需要认证（管理员）

**路径参数**:
| 字段 | 类型 | 说明 |
|------|------|------|
| sessionId | string | 会话ID |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "sessionId": "sess_abc123",
    "userId": 1,
    "username": "zhangsan",
    "device": "MacBook",
    "os": "macOS",
    "durationMs": 45000,
    "nodes": [
      {
        "id": "n1",
        "eventType": "PAGE_LOAD",
        "timestamp": 1712644800000,
        "payload": { "referrer": "direct" }
      },
      {
        "id": "n2",
        "eventType": "LLM_QUERY",
        "timestamp": 1712644801000,
        "payload": { "prompt": "如何用Java实现单例模式？", "turnId": "t1" }
      },
      {
        "id": "n3",
        "eventType": "LLM_RESPONSE",
        "timestamp": 1712644803000,
        "payload": { "responseText": "单例模式是...", "ttftMs": 500 }
      },
      {
        "id": "n4",
        "eventType": "ERROR",
        "timestamp": 1712644850000,
        "payload": { "errorType": "TIMEOUT", "errorMessage": "API超时" },
        "isError": true
      }
    ],
    "edges": [
      { "source": "n1", "target": "n2", "type": "sequence" },
      { "source": "n2", "target": "n3", "type": "sequence" },
      { "source": "n3", "target": "n4", "type": "sequence" }
    ]
  },
  "message": "ok"
}
```

**节点字段说明**:

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 节点唯一ID |
| eventType | string | 事件类型 |
| timestamp | int | 事件时间戳 |
| payload | object | 事件数据 |
| isError | bool | 是否错误节点（可选） |

**边类型说明**:

| 边类型 | 说明 |
|--------|------|
| sequence | 正常连续交互 |
| jump | 时间间隔超过30分钟 |
| error | 从错误节点出发的边 |

---

### 5.3 流失漏斗

**接口**: `GET /api/v1/insight/funnel/drop-off`

**描述**: 获取多轮对话的流失漏斗数据

**请求头**: 需要认证（管理员）

**查询参数**:
| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| startDate | string | 否 | 开始日期 YYYY-MM-DD |
| endDate | string | 否 | 结束日期 YYYY-MM-DD |

**成功响应** (200):
```json
{
  "status": 200,
  "data": {
    "funnel": [
      { "turn": 1, "count": 100, "rate": 1.0 },
      { "turn": 2, "count": 85, "rate": 0.85 },
      { "turn": 3, "count": 60, "rate": 0.60 },
      { "turn": 4, "count": 35, "rate": 0.35 }
    ],
    "dropOffReasons": [
      {
        "fromTurn": 2,
        "toTurn": 3,
        "reason": "回复太慢",
        "count": 15,
        "percentage": 0.25
      },
      {
        "fromTurn": 2,
        "toTurn": 3,
        "reason": "无法理解上下文",
        "count": 10,
        "percentage": 0.17
      }
    ]
  },
  "message": "ok"
}
```

---
## 6. 数据结构定义

### 6.1 User（用户）

```typescript
interface User {
  userId: number;      // 用户ID
  username: string;    // 用户名
  createdAt: string;   // 创建时间 ISO8601
}
```

### 6.2 Session（会话）

```typescript
interface Session {
  sessionId: string;   // 会话ID
  userId: number;      // 所属用户ID
  title: string;       // 会话标题
  status: string;      // 状态: active/completed
  createdAt: string;   // 创建时间
  updatedAt: string;   // 更新时间
  messageCount?: number; // 消息数量（列表时返回）
}
```

### 6.3 Message（消息）

```typescript
interface Message {
  id: number;          // 消息ID
  sessionId: string;   // 所属会话ID
  role: string;        // 角色: user/ai
  content: string;     // 消息内容
  createdAt: string;   // 创建时间
}
```

### 6.4 TrackEvent（行为事件）

```typescript
interface TrackEvent {
  eventId: string;       // 事件唯一ID
  sessionId: string;     // 会话ID
  userId?: number;       // 用户ID（从Token解析）
  eventType: string;     // 事件类型
  turnId?: string;       // 对话轮次
  timestamp: number;     // 事件时间戳
  payload: object;      // 事件数据
  context?: {            // 上下文
    device?: string;
    os?: string;
    browser?: string;
    referrer?: string;
  };
}
```

### 6.5 JourneyNode（旅程节点）

```typescript
interface JourneyNode {
  id: string;           // 节点ID
  eventType: string;    // 事件类型
  timestamp: number;    // 时间戳
  payload: object;     // 事件数据
  isError?: boolean;    // 是否错误节点
}
```

### 6.6 JourneyEdge（旅程边）

```typescript
interface JourneyEdge {
  source: string;      // 源节点ID
  target: string;      // 目标节点ID
  type: string;        // 边类型: sequence/jump/error
}
```

---

## 7. 附录：错误码汇总

| 错误码 | 说明 | HTTP状态码 |
|--------|------|------------|
| 1001 | 用户名已存在 | 400 |
| 1002 | 用户名或密码错误 | 400 |
| 1003 | Token已过期 | 401 |
| 2001 | 会话不存在 | 404 |
| 2002 | 会话不属于该用户 | 403 |
| 3001 | 事件格式错误 | 400 |
| 3002 | 事件类型不支持 | 400 |
