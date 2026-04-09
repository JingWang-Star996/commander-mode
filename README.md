# Commander Mode — Skill for OpenClaw

> **Solve the "AI silence" problem:** When you give a complex task, the AI goes silent for minutes before responding. Commander Mode fixes this.

**Production verified.** Not an official OpenClaw feature — a community practice born from real production pain.

---

## The Problem

Before Commander Mode:

```
User: "Analyze this month's attendance data and generate a report"
         ⏳ ... 40 seconds of silence ...
         ⏳ ... still no response ...
         ⏳ ... the AI is busy making 40+ tool calls ...
         ✅ Finally: "Done! Here's the report."
```

The LLM's default behavior is **think/execute → output**. The user perceives this as **waiting forever**.

## The Solution

Commander Mode enforces a simple rule: **respond first, then dispatch.**

```
User sends task
    ↓ (within 3 seconds)
① Main session: responds to user immediately
    "Got it, processing now. Expected: 3 minutes."
    ↓ (simultaneously)
② Main session: spawns subagents for heavy work
    ↓
③ Subagents: execute in parallel
    ↓
④ Results auto-pushed back → main session forwards to user
```

**Result: response time drops from ~40s to ~3s.** Tool calls in main session drop from 40+ to ≤3.

---

## Core Mechanisms

### 1. Reply-First-Then-Dispatch (P0)

The main session **must** reply to the user before any tool call. This is the single most important rule.

### 2. Agent Idle Counter

Every reply includes a counter `[🤖x/5]` showing available subagent slots (out of 5 concurrent). This prevents the main session from forgetting to delegate when agents are free.

### 3. Parallel Subagent Dispatch

Independent tasks run in parallel subagents. Dependent tasks run serially with result passing. The main session never does heavy lifting.

---

## Included Rules

| Rule | What It Does |
|------|-------------|
| **Reply-First (P0)** | 3-second response guarantee, before any tool call |
| **Agent Counter** | `[🤖x/5]` tracking — prevents main session from doing subagent work |
| **Push-Based Completion** | Subagents auto-report; main session immediately forwards (no polling) |
| **Read/Write Separation** | ReadWorkers (read-only, parallel-safe) vs WriteWorkers (serial) |
| **Flexible Recovery** | Graceful handling of subagent failures with retry logic |
| **Continue vs Spawn** | Decision guide: when to continue a conversation vs spawn fresh |
| **Scratchpad Sharing** | Shared state file for multi-subagent collaboration |
| **Tool Permissions** | Explicit allow/deny lists per subagent type |
| **4-Stage Workflow** | Analyze → Collect → Execute → Verify (for complex tasks) |
| **Discussion vs Command** | Distinguish exploratory discussion from execution orders |
| **Feishu Doc Sync** | Rules for reading/writing Feishu docs through subagents |

---

## Installation

1. Copy `SKILL.md` to your OpenClaw skills directory:
   ```bash
   cp SKILL.md ~/.openclaw/workspace/skills/commander-mode/SKILL.md
   ```
2. Restart OpenClaw (or wait for next skill reload).
3. The skill will be auto-loaded for tasks matching its description.

## Quick Reference

```
Got a task?
  1️⃣ Reply to user (3 seconds)
  2️⃣ Simple? → do it yourself
     Complex? → spawn subagent
  3️⃣ Dependent? → serial | Independent? → parallel
  4️⃣ Subagent done → forward immediately
```

---

## When to Use

- **Complex data processing** (attendance analysis, retention metrics)
- **Batch document operations** (bulk create, bulk update Feishu docs)
- **Multi-step workflows** (fetch data → analyze → write report → publish)
- **Any task requiring >3 tool calls**

## When NOT to Use

- Simple queries ("what's the time?")
- Single tool call tasks
- Forwarding a message

---

## Why This Exists

OpenClaw has built-in subagent support, but doesn't enforce the **reply-first discipline** by default. Without it, the LLM will still default to execute-then-respond — causing long silences. Commander Mode patches this gap with explicit rules and the agent counter as a safety net.

Think of it as: **OpenClaw provides the pipes. Commander Mode provides the discipline.**

---

## License

MIT. See [LICENSE](LICENSE).

## Changelog

See [CHANGELOG.md](CHANGELOG.md).
