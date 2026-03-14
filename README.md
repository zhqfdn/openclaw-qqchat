# QQChat - OpenClaw QQ 插件

<p align="center">
  <strong>通过 NapCat WebSocket 连接 QQ 机器人</strong>
</p>

<p align="center">
  支持私聊、群聊、图片、回复等多种消息类型
</p>

---

## 功能特性

- ✅ 私聊支持 - QQ 个人消息收发
- ✅ 群聊支持 - QQ 群消息收发
- ✅ @模式 - 群聊只有被@才回复
- ✅ 戳一戳 - 支持戳一戳事件
- ✅ 媒体消息 - 图片、语音、文件收发
- ✅ 自动重连 - WebSocket 断线自动重连
- ✅ 心跳检测 - 连接健康状态监控
- ✅ Agent 绑定 - 支持不同 QQ 号/群绑定不同 Agent

---

## 快速开始

### 1. 安装 NapCat

参考 [NapCat 官方文档](https://github.com/NapNeko/NapCatQQ) 安装并配置。

### 2. 配置 NapCat WebSocket

在 NapCat 的 `config.yml` 中启用 WebSocket：

```yaml
ws:
  servers:
    - url: ws://0.0.0.0:3001
      token: "your-token"  # 可选，建议设置
      enableHeart: true
```

### 3. 配置 OpenClaw

在 `openclaw.json` 中添加配置：

```json
{
  "channels": {
    "qqchat": {
      "wsUrl": "ws://127.0.0.1:3001",
      "accessToken": "your-token",
      "enabled": true
    }
  }
}
```

### 4. 启动

```bash
openclaw gateway restart
```

---

## 配置详解

### 完整配置示例

```json
{
  "channels": {
    "qqchat": {
      "wsUrl": "ws://127.0.0.1:3001",
      "accessToken": "your-token",
      "enabled": true,
      "groupAtMode": true,
      "groupHistoryLimit": 20,
      "chatAgentMap": {
        "88325467": "main",
        "3541849": "hyhome"
      }
    }
  }
}
```

### 配置项说明

| 配置项 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| `wsUrl` | string | ✅ | - | NapCat WebSocket 地址 |
| `accessToken` | string | ❌ | "" | 访问令牌，需与 NapCat 配置一致 |
| `enabled` | boolean | ❌ | true | 是否启用 |
| `groupAtMode` | boolean | ❌ | true | 群聊时是否只有被@才回复 |
| `groupHistoryLimit` | number | ❌ | 20 | 群聊历史消息条数 |
| `chatAgentMap` | object | ❌ | {} | QQ号/群号 → Agent 映射 |

---

## chatAgentMap 使用

### 作用

将不同的 QQ 号或群号绑定到不同的 Agent，实现多 Agent 分工。

### 配置格式

```json
{
  "chatAgentMap": {
    "QQ号或群号": "Agent名称"
  }
}
```

### 示例

```json
{
  "chatAgentMap": {
    "88325467": "main",
    "275477425": "hydev",
    "3541849": "hyhome"
  }
}
```

**说明：**
- `88325467`（私聊）→ 路由到 `main` Agent
- `275477425`（私聊）→ 路由到 `hydev` Agent
- `3541849`（群聊）→ 路由到 `hyhome` Agent

### 路由优先级

```
消息进来
  ↓
查 chatAgentMap（插件配置）← 优先
  → 命中 → 使用映射的 Agent
  ↓ 未命中
查 OpenClaw 顶层 bindings 配置
  → 命中/未命中 → 走默认逻辑（默认 main Agent）
```

---

## 消息目标格式

发送消息时使用以下格式：

| 类型 | 格式 | 示例 |
|------|------|------|
| 私聊 | `private:<QQ号>` | `qq:private:88325467` |
| 群聊 | `group:<群号>` | `qq:group:3541849` |

---

## CLI 命令

### 发送消息

```bash
# 私聊
openclaw message send "你好" --to qq:private:88325467

# 群聊
openclaw message send "大家好" --to qq:group:3541849
```

### 查看状态

```bash
# 查看通道状态
openclaw channels

# 查看日志
openclaw logs --channel qqchat
```

### 重启网关

```bash
openclaw gateway restart
```

---

## 支持的消息类型

| 类型 | 接收 | 发送 | 说明 |
|------|------|------|------|
| 文本 | ✅ | ✅ | 普通文字消息 |
| @ | ✅ | ✅ | @某人 或 @全体成员 |
| 图片 | ✅ | ✅ | 支持 URL 和文件 |
| 表情 | ✅ | ❌ | QQ 表情 |
| 回复 | ✅ | ✅ | 回复指定消息 |
| 语音 | ✅ | ✅ | 音频消息 |
| 文件 | ✅ | ✅ | 文件消息 |
| JSON | ✅ | ❌ | 富文本消息 |

---

## 常见问题

### 连接失败

1. 检查 NapCat 是否启动：`curl http://localhost:3001/get_status`
2. 确认 `wsUrl` 配置正确
3. 检查防火墙是否放行端口

### 消息没有收到

1. 确认 `enabled: true`
2. 群聊检查是否开启了 `groupAtMode`（需要@ bot 才会回复）

### Agent 绑定不生效

1. 确认 `chatAgentMap` 格式正确
2. 检查 Agent 名称是否正确（区分大小写）
3. 查看日志确认是否命中绑定

---

## 相关链接

- [OpenClaw 文档](https://docs.openclaw.ai/)
- [NapCat GitHub](https://github.com/NapNeko/NapCatQQ)
- [OneBot 11 协议](https://github.com/botuniverse/onebot-11)

---

## 开源协议

MIT © qcluffy
