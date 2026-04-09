# Changelog

All notable changes to Commander Mode will be documented in this file.

## [Unreleased]

### 2026-04-10 — Major Upgrade

**Source**: Real production incidents and lessons learned on 2026-04-09/10.

#### Added
- **Agent Idle Counter mechanism** (`[🤖x/5]` marker) — prevents main session from forgetting to delegate when subagents are free
- **3 Meta-Rules** for SOUL.md (~50 tokens) — top-priority rules that must never be forgotten
- **Orchestra 7 Integration Rules**:
  1. Push-based task notification system
  2. Read/Write separation (ReadWorker / WriteWorker)
  3. Flexible recovery for subagent failures
  4. Continue vs Spawn decision guide
  5. Scratchpad knowledge sharing
  6. Tool permission control per subagent type
  7. 4-Stage Workflow (Analyze → Collect → Execute → Verify)
- **Discussion vs Command** distinction rule — separate exploratory discussion from execution orders
- **Feishu document sync rules** — reading/writing Feishu docs through subagents with async patterns
- **Quick Reference Card** — 5-second decision checklist for main session
- **Full workflow simulation** — end-to-end example with violation checks

#### Fixed
- **Counter anti-loss bug** — added hard rule: counter marker must follow substantive content, cannot replace it. Violation = INC incident.
- Fixed edge case where main session would send only `[🤖x/5]` without any actual response

#### Changed
- Strengthened P0 rules: reply sequence check is now the very first item in the checklist
- Added explicit counter format examples (correct vs incorrect)

#### Impact
- Response time: ~40s → ~3s
- Main session tool calls: 40+ → ≤3
- Subagent parallel capacity: 5 concurrent agents

### 2026-04-09 — Initial Version

- Core "reply-first-then-dispatch" rule
- Commander = dispatcher, not worker principle
- Task type routing table
- sessions_spawn templates
- Initial incident record (INC-2026-0409-007)
