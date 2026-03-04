---
title: "从 WhatsApp 脚本到 AI Agent 平台：OpenClaw 四个月演进全景"
date: 2026-03-04
draft: false
isCJKLanguage: true
tags: ["AI", "OpenClaw", "Agent", "开源", "产品演进"]
---

> 基于 68 个版本（v0.1.0 → v2026.3.1）的源码分析，复盘一个 AI 产品从 0 到 1 的真实路径。

---

## 引言

2025 年 11 月，一个叫 `warelay` 的项目悄然诞生。四个月后，它变成了 `OpenClaw`——一个支持 8 种消息渠道、原生三端应用、子代理协作的 AI Agent 平台。

这不是一个预设宏大架构然后填充的故事。这是一个从 **400 行 WhatsApp 转发脚本** 长出来的产品演进实录。

我分析了 OpenClaw 从第一个 commit 到当前最新版的全部 68 个 release，追踪了约 150 万行代码变更，试图回答一个问题：

**一个 AI 产品是如何找到自己的？**

---

## 第一章：工具期——"让消息能转发就行"

### v0.1.0：warelay 的诞生

2025 年 11 月 25 日，项目以 `warelay`（WhatsApp relay）的名字发布。

```json
{
  "name": "warelay",
  "description": "WhatsApp relay CLI (send, monitor, webhook, auto-reply) using Twilio"
}
```

此时的定位极其清晰：**一个 WhatsApp 消息转发的命令行工具**。

核心能力：
- 通过 Twilio API 发送 WhatsApp 消息
- 通过 Baileys 库直接连接 WhatsApp Web
- 支持 Webhook 接收入站消息
- 支持配置驱动的自动回复

代码规模：**4,227 行**（不含测试）。

架构极其简单：

```
CLI 入口 → 命令解析 → Twilio/Baileys 调用 → 返回结果
```

值得注意的是，即使在这个最早期版本，已经有一个关键设计：**双 Provider 策略**。

```typescript
// v0.1.0 的 provider 选择逻辑
export function pickProvider(explicit: Provider | "auto"): Provider {
  if (explicit !== "auto") return explicit;
  return webAuthExists() ? "web" : "twilio";
}
```

这个看似简单的抽象，为后来的多渠道扩展埋下了伏笔。

### v0.1.1 - v0.1.3：打磨期

接下来的三个版本是典型的"让它真正能用"阶段：

- **v0.1.1**：修复 npx 执行失败（ESM 入口问题）
- **v0.1.2**：修复 Commander.js 类型错误
- **v0.1.3**：引入 pino 结构化日志

这些版本的共同特点是：**没有新功能，全是修复和打磨**。

一个有趣的细节：v0.1.2 只改了 15 行代码，但它暴露了一个流程问题——v0.1.1 带着类型错误就发布了，说明当时没有 CI 门禁。这个问题在后续版本中被修复。

### 工具期的启示

此时的 warelay 没有任何"AI Agent"的概念。它就是一个消息中间件。用户的心智模型是：

> "我需要一个工具帮我转发 WhatsApp 消息。"

这个定位简单、清晰、可验证。

---

## 第二章：代理期——"让 AI 能主动做事"

### v1.2.0：Heartbeat 的引入

2025 年 12 月中旬，一个关键功能出现了：**Heartbeat（心跳）**。

这不是一个技术上复杂的功能，但它标志着产品定位的根本转变。

```typescript
// v1.2.0 的 HEARTBEAT_OK 协议
if (trimmedReply === "HEARTBEAT_OK") {
  logVerbose("Agent responded HEARTBEAT_OK, skipping send");
  return; // 不发送消息给用户
}
```

在此之前，系统是**被动响应**的：用户发消息 → AI 回复。

有了 Heartbeat，系统变成**主动轮询**的：定时唤醒 AI → AI 决定是否需要打扰用户。

这个转变的深远意义在于：**AI 从"工具"变成了"代理"**。

工具等待被调用。代理主动行动。

配套的设计也很精妙：

```typescript
// 心跳跳过时不刷新会话时间戳
if (response === "HEARTBEAT_OK") {
  // 不更新 session.updatedAt
  // 保持空闲过期逻辑正常工作
}
```

这说明设计者已经在思考"AI 代理的自主性边界"——让 AI 能主动联系用户，但不能通过无意义的心跳来"霸占"会话。

### v1.3.0：多 Agent 架构

如果说 Heartbeat 是 Agent 化的起点，v1.3.0 则是 Agent 化的确认。

这个版本引入了**可插拔的 Agent 架构**：

```typescript
// v1.3.0 的 Agent 接口
export interface AgentSpec {
  kind: AgentKind;
  isInvocation: (argv: string[]) => boolean;
  buildArgs: (ctx: BuildArgsContext) => string[];
  parseOutput: (rawStdout: string) => AgentParseResult;
}
```

支持的 Agent：
- Claude（Anthropic）
- Codex（OpenAI）
- Pi（开源）
- Opencode（开源）

这个抽象的出现说明：**warelay 不再是一个"调用 Claude 的 WhatsApp 工具"，而是一个"可以调用任意 AI Agent 的平台"**。

### 代理期的定位

此时的产品定位变成了：

> "一个让 AI Agent 能通过 WhatsApp 与用户交互的平台。"

用户的心智模型也发生了变化：

> "我有一个 AI 助手，它通过 WhatsApp 找我。"

---

## 第三章：平台期——"让更多渠道接入"

### v2.0.0-beta1：架构革命

2026 年 1 月初，项目迎来了最大的一次重构。

变更规模：**+160,623 行 / -8,041 行**。

三个标志性变化：

**1. 项目改名：warelay → clawdis**

名字从"WhatsApp relay"变成了"Clawd is"——强调 AI 代理的身份，而不是消息转发的功能。

**2. 架构转型：CLI → Gateway**

```
旧架构：CLI 命令 → 直接执行 → 返回
新架构：CLI/App → WebSocket → Gateway → 执行 → 推送
```

Gateway 成为核心枢纽，所有客户端（CLI、Web、移动端）都通过 WebSocket 连接到它。

**3. Twilio 完全移除**

一个大胆的决定：**删除了最初的核心依赖**。

原因是 Twilio 的 WhatsApp API 有诸多限制（24 小时窗口、模板消息等），而 Baileys 直连方案已经足够成熟。

这个决定体现了产品思维的成熟：**不是功能越多越好，而是聚焦核心路径**。

### v2.0.0-beta3：Discord 加入

Discord 是第一个非 WhatsApp 渠道。

```typescript
// v2.0.0-beta3 的 Discord transport
export class DiscordTransport implements ChannelTransport {
  // ... 实现 ChannelTransport 接口
}
```

关键设计：**ChannelTransport 接口**。

所有渠道都实现同一个接口，Gateway 不需要知道消息来自哪个渠道。这个抽象为后续的渠道爆发奠定了基础。

### v2.0.0-beta5：渠道爆发

这个版本在一次更新中加入了三个新渠道：

- **Signal**：通过 signal-cli daemon
- **iMessage**：macOS 原生集成
- **Gateway TUI**：终端内聊天界面

同时还加入了 **Talk Mode**（语音对话模式），使用 ElevenLabs TTS。

变更规模：**+37,719 行**。

此时支持的渠道已达 5 个：WhatsApp、Telegram、Discord、Signal、iMessage。

### 平台期的定位

产品定位再次升级：

> "一个让 AI Agent 通过任意渠道与用户交互的统一平台。"

用户的心智模型：

> "我有一个 AI 助手，它在我所有的聊天软件里都能找到我。"

---

## 第四章：产品期——"让它成为真正的产品"

### v2026.1.5：巨石拆分

CalVer 版本号的启用标志着项目进入"产品期"。

这个版本的主要工作是**还技术债**：

```
server.ts        6,259 行 → 19 个模块
clawdis-tools.ts 2,316 行 → 24 个工具模块
config.ts        1,754 行 → 12 个配置子模块
```

同时，项目再次改名：**clawdis → clawdbot**。

### v2026.1.8：安全加固

这是一个纯安全版本，没有新功能。

核心变化：
- DM 默认需要配对审批
- Sandbox 默认容器隔离
- Groups 白名单模式

设计哲学从"默认信任"变成"默认拒绝"。

### v2026.2.1：开源化

项目最终定名：**openclaw**。

这个名字的选择很有意思：
- "Open"强调开源
- "Claw"保留了品牌延续性（Clawd → Claw）

同时，这个版本开始大规模的中文文档工作，说明目标用户群在扩展。

### v2026.2.17：子代理系统成熟

最后一个重大架构变化是**嵌套子代理系统**：

```typescript
// v2026.2.17 的子代理配置
{
  "subagents": {
    "maxSpawnDepth": 3,  // 最多 3 层嵌套
    "maxConcurrent": 8   // 最多 8 个并发
  }
}
```

配套的循环检测机制：

```typescript
// 4 种循环检测器
- ExactMatchDetector     // 完全匹配
- SemanticSimilarityDetector  // 语义相似
- ToolPatternDetector    // 工具调用模式
- OutputPatternDetector  // 输出模式
```

这说明产品已经在思考**多 Agent 协作**的场景。

### 产品期的定位

最终的产品定位：

> "一个开源的 AI Agent 运行时平台，支持多渠道、多端、多代理协作。"

---

## 四个月的定位演进总结

| 时期 | 版本 | 名称 | 定位 | 核心能力 |
|------|------|------|------|----------|
| 工具期 | v0.1.x | warelay | WhatsApp 消息转发工具 | Twilio/Baileys 双 Provider |
| 代理期 | v1.x | warelay | AI Agent 交互平台 | Heartbeat、多 Agent |
| 平台期 | v2.0.0-beta | clawdis | 多渠道统一平台 | Gateway、5+ 渠道 |
| 产品期 | v2026.x | clawdbot → openclaw | 开源 AI 运行时 | 安全、子代理、多端 |

每次定位升级都伴随着：
1. 名字变化（反映心智模型的变化）
2. 架构重构（支撑新的规模和复杂度）
3. 删除旧代码（聚焦核心路径）

---

## 展望：OpenClaw 的下一步

基于代码演进的趋势，我对 OpenClaw 的未来有以下预判：

### 1. Agent-to-Agent 协作将成为核心

v2026.2.17 的嵌套子代理系统只是开始。下一步可能是：
- Agent 之间的消息传递协议
- Agent 能力发现和注册
- 分布式 Agent 执行

### 2. 安全将继续强化

从 v2026.1.8 开始的"默认拒绝"哲学会继续深化：
- 更细粒度的权限控制
- 沙箱逃逸检测
- 审计日志

### 3. 移动端会追赶 macOS

当前 macOS 的体验明显领先（Talk Mode、原生 UI）。Android 的 Compose 重构说明移动端在追赶。预计：
- iOS 会加入更多原生功能
- Android 会达到功能对等
- 可能会有独立的移动端 Agent 能力

### 4. 可能会有企业版

开源化（openclaw）通常意味着商业化的前奏。可能的企业功能：
- 多租户
- SSO 集成
- 合规审计
- SLA 保障

### 5. 协议标准化

当前 8 种渠道的接入都是定制实现。如果要继续扩展，可能需要：
- 标准化的 Channel 协议
- 第三方渠道插件机制
- 可能会拥抱 MCP（Model Context Protocol）等行业标准

---

## 结语

OpenClaw 的演进故事给我最大的启发是：

**好的产品不是设计出来的，是长出来的。**

它没有一开始就宣称要做"AI Agent 平台"。它从一个具体的痛点（WhatsApp 消息转发）开始，在解决问题的过程中，逐渐发现了更大的可能性。

每一次定位升级都不是空想，而是被代码变更证实的：
- Heartbeat 的出现证实了"代理化"
- ChannelTransport 的抽象证实了"平台化"
- 子代理系统的成熟证实了"协作化"

这可能是所有 AI 产品都需要走的路：**从工具到代理，从单点到平台，从封闭到开放**。

OpenClaw 只是走得比较快而已。

---

*本文基于 68 个版本的源码分析，分析代码和详细报告见 [moltlog](https://github.com/zhixian/moltlog) 项目。*
