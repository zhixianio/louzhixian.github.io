---
title: "From WhatsApp Script to AI Agent Platform: OpenClaw's Four-Month Evolution"
date: 2026-03-04
draft: false
tags: ["AI", "OpenClaw", "Agent", "Open Source", "Product Evolution"]
cover:
  image: 'cover.png'
  alt: 'OpenClaw Evolution'
  relative: true
---

> A source code analysis of 68 releases (v0.1.0 → v2026.3.1), tracing an AI product's real journey from 0 to 1.

---

## Introduction

In November 2025, a project called `warelay` quietly came into existence. Four months later, it became `OpenClaw`—an AI Agent platform supporting 8 messaging channels, native apps on three platforms, and sub-agent collaboration.

This isn't a story of a grand architecture being designed upfront and filled in later. This is a product evolution that **grew from a 400-line WhatsApp relay script**.

I analyzed all 68 releases of OpenClaw from its first commit to the latest version, tracking approximately 1.5 million lines of code changes, trying to answer one question:

**How does an AI product find itself?**

---

## Chapter 1: Tool Phase—"Just Make the Messages Forward"

### v0.1.0: The Birth of warelay

On November 25, 2025, the project was released under the name `warelay` (WhatsApp relay).

```json
{
  "name": "warelay",
  "description": "WhatsApp relay CLI (send, monitor, webhook, auto-reply) using Twilio"
}
```

The positioning was crystal clear: **a command-line tool for WhatsApp message relay**.

Core capabilities:
- Send WhatsApp messages via Twilio API
- Connect directly to WhatsApp Web via Baileys library
- Receive inbound messages via Webhook
- Configuration-driven auto-reply

Code size: **4,227 lines** (excluding tests).

The architecture was extremely simple:

```
CLI entry → Command parsing → Twilio/Baileys call → Return result
```

Notably, even in this earliest version, there was a key design: **Dual Provider Strategy**.

```typescript
// v0.1.0 provider selection logic
export function pickProvider(explicit: Provider | "auto"): Provider {
  if (explicit !== "auto") return explicit;
  return webAuthExists() ? "web" : "twilio";
}
```

This seemingly simple abstraction laid the groundwork for later multi-channel expansion.

### v0.1.1 - v0.1.3: Polish Phase

The next three versions were typical "make it actually work" releases:

- **v0.1.1**: Fix npx execution failure (ESM entry issue)
- **v0.1.2**: Fix Commander.js type errors
- **v0.1.3**: Introduce pino structured logging

The common theme: **no new features, all fixes and polish**.

An interesting detail: v0.1.2 changed only 15 lines of code, but it revealed a process issue—v0.1.1 shipped with type errors, indicating no CI gate at the time. This was fixed in later versions.

### Tool Phase Insights

At this point, warelay had no concept of "AI Agent." It was just a message middleware. The user's mental model was:

> "I need a tool to help me relay WhatsApp messages."

This positioning was simple, clear, and verifiable.

---

## Chapter 2: Agent Phase—"Let AI Act Proactively"

### v1.2.0: Introduction of Heartbeat

In mid-December 2025, a crucial feature appeared: **Heartbeat**.

This wasn't technically complex, but it marked a fundamental shift in product positioning.

```typescript
// v1.2.0 HEARTBEAT_OK protocol
if (trimmedReply === "HEARTBEAT_OK") {
  logVerbose("Agent responded HEARTBEAT_OK, skipping send");
  return; // Don't send message to user
}
```

Before this, the system was **passive reactive**: User sends message → AI replies.

With Heartbeat, the system became **proactive polling**: Periodically wake AI → AI decides whether to bother user.

The profound significance: **AI transformed from "tool" to "agent"**.

Tools wait to be called. Agents act proactively.

The accompanying design was elegant:

```typescript
// Don't refresh session timestamp on heartbeat skip
if (response === "HEARTBEAT_OK") {
  // Don't update session.updatedAt
  // Keep idle expiration logic working
}
```

This shows the designers were already thinking about "AI agent autonomy boundaries"—letting AI proactively contact users, but preventing meaningless heartbeats from "hogging" sessions.

### v1.3.0: Multi-Agent Architecture

If Heartbeat was the starting point of Agent-ization, v1.3.0 was its confirmation.

This version introduced a **pluggable Agent architecture**:

```typescript
// v1.3.0 Agent interface
export interface AgentSpec {
  kind: AgentKind;
  isInvocation: (argv: string[]) => boolean;
  buildArgs: (ctx: BuildArgsContext) => string[];
  parseOutput: (rawStdout: string) => AgentParseResult;
}
```

Supported Agents:
- Claude (Anthropic)
- Codex (OpenAI)
- Pi (open source)
- Opencode (open source)

This abstraction's emergence shows: **warelay was no longer "a WhatsApp tool that calls Claude," but "a platform that can call any AI Agent"**.

### Agent Phase Positioning

The product positioning became:

> "A platform for AI Agents to interact with users via WhatsApp."

The user's mental model also changed:

> "I have an AI assistant that reaches me through WhatsApp."

---

## Chapter 3: Platform Phase—"Let More Channels Connect"

### v2.0.0-beta1: Architectural Revolution

In early January 2026, the project underwent its biggest refactoring.

Change scale: **+160,623 lines / -8,041 lines**.

Three landmark changes:

**1. Project Rename: warelay → clawdis**

The name changed from "WhatsApp relay" to "Clawd is"—emphasizing AI agent identity rather than message relay functionality.

**2. Architecture Transformation: CLI → Gateway**

```
Old architecture: CLI command → Direct execution → Return
New architecture: CLI/App → WebSocket → Gateway → Execute → Push
```

Gateway became the core hub, with all clients (CLI, Web, mobile) connecting via WebSocket.

**3. Complete Twilio Removal**

A bold decision: **removed the original core dependency**.

The reason was Twilio's WhatsApp API had many limitations (24-hour window, template messages, etc.), while the Baileys direct connection was mature enough.

This decision reflects product thinking maturity: **more features isn't better; focus on the core path**.

### v2.0.0-beta3: Discord Joins

Discord was the first non-WhatsApp channel.

```typescript
// v2.0.0-beta3 Discord transport
export class DiscordTransport implements ChannelTransport {
  // ... implements ChannelTransport interface
}
```

Key design: **ChannelTransport interface**.

All channels implement the same interface; Gateway doesn't need to know where messages come from. This abstraction laid the foundation for subsequent channel explosion.

### v2.0.0-beta5: Channel Explosion

This version added three new channels in one update:

- **Signal**: via signal-cli daemon
- **iMessage**: macOS native integration
- **Gateway TUI**: In-terminal chat interface

Also added **Talk Mode** (voice conversation mode) using ElevenLabs TTS.

Change scale: **+37,719 lines**.

Supported channels now reached 5: WhatsApp, Telegram, Discord, Signal, iMessage.

### Platform Phase Positioning

Product positioning upgraded again:

> "A unified platform for AI Agents to interact with users via any channel."

User mental model:

> "I have an AI assistant that can find me in all my chat apps."

---

## Chapter 4: Product Phase—"Make It a Real Product"

### v2026.1.5: Monolith Split

The adoption of CalVer versioning marked the project's entry into "product phase."

This version's main work was **paying down tech debt**:

```
server.ts        6,259 lines → 19 modules
clawdis-tools.ts 2,316 lines → 24 tool modules
config.ts        1,754 lines → 12 config submodules
```

Also, another rename: **clawdis → clawdbot**.

### v2026.1.8: Security Hardening

This was a pure security release with no new features.

Core changes:
- DMs require pairing approval by default
- Sandbox defaults to container isolation
- Groups use whitelist mode

Design philosophy shifted from "default trust" to "default deny."

### v2026.2.1: Going Open Source

The project's final name: **openclaw**.

An interesting name choice:
- "Open" emphasizes open source
- "Claw" preserves brand continuity (Clawd → Claw)

This version also began extensive Chinese documentation work, indicating target user expansion.

### v2026.2.17: Sub-agent System Maturation

The final major architectural change was the **nested sub-agent system**:

```typescript
// v2026.2.17 sub-agent configuration
{
  "subagents": {
    "maxSpawnDepth": 3,  // Maximum 3 levels of nesting
    "maxConcurrent": 8   // Maximum 8 concurrent
  }
}
```

Accompanying loop detection mechanisms:

```typescript
// 4 types of loop detectors
- ExactMatchDetector     // Exact match
- SemanticSimilarityDetector  // Semantic similarity
- ToolPatternDetector    // Tool call patterns
- OutputPatternDetector  // Output patterns
```

This shows the product was thinking about **multi-Agent collaboration** scenarios.

### Product Phase Positioning

Final product positioning:

> "An open-source AI Agent runtime platform supporting multi-channel, multi-platform, multi-agent collaboration."

---

## Four Months of Positioning Evolution Summary

| Phase | Version | Name | Positioning | Core Capability |
|-------|---------|------|-------------|-----------------|
| Tool | v0.1.x | warelay | WhatsApp message relay tool | Twilio/Baileys dual Provider |
| Agent | v1.x | warelay | AI Agent interaction platform | Heartbeat, multi-Agent |
| Platform | v2.0.0-beta | clawdis | Multi-channel unified platform | Gateway, 5+ channels |
| Product | v2026.x | clawdbot → openclaw | Open source AI runtime | Security, sub-agents, multi-platform |

Each positioning upgrade was accompanied by:
1. Name changes (reflecting mental model shifts)
2. Architecture refactoring (supporting new scale and complexity)
3. Old code deletion (focusing on core path)

---

## Looking Ahead: OpenClaw's Next Steps

Based on code evolution trends, here are my predictions for OpenClaw's future:

### 1. Agent-to-Agent Collaboration Will Become Core

v2026.2.17's nested sub-agent system is just the beginning. Next steps might include:
- Inter-Agent message passing protocols
- Agent capability discovery and registration
- Distributed Agent execution

### 2. Security Will Continue Strengthening

The "default deny" philosophy from v2026.1.8 will deepen:
- More fine-grained permission control
- Sandbox escape detection
- Audit logging

### 3. Mobile Will Catch Up to macOS

Currently macOS experience leads significantly (Talk Mode, native UI). Android's Compose rewrite shows mobile is catching up. Expect:
- iOS will add more native features
- Android will reach feature parity
- Possibly standalone mobile Agent capabilities

### 4. Enterprise Edition Possible

Open sourcing (openclaw) often precedes commercialization. Possible enterprise features:
- Multi-tenancy
- SSO integration
- Compliance auditing
- SLA guarantees

### 5. Protocol Standardization

Current 8 channel integrations are all custom implementations. For continued expansion, needs might include:
- Standardized Channel protocol
- Third-party channel plugin mechanism
- Possibly embracing industry standards like MCP (Model Context Protocol)

---

## Conclusion

The biggest insight from OpenClaw's evolution story:

**Good products aren't designed, they're grown.**

It didn't start by claiming to be an "AI Agent platform." It started from a specific pain point (WhatsApp message relay) and gradually discovered larger possibilities while solving problems.

Each positioning upgrade wasn't speculation but confirmed by code changes:
- Heartbeat's emergence confirmed "Agent-ization"
- ChannelTransport abstraction confirmed "Platform-ization"
- Sub-agent system maturation confirmed "Collaboration-ization"

This might be the path all AI products need to take: **from tool to agent, from single point to platform, from closed to open**.

OpenClaw just happens to be moving faster.

---

*This article is based on source code analysis of 68 releases. Analysis code and detailed reports can be found in the [moltlog](https://github.com/zhixian/moltlog) project.*
