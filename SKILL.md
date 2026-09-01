---
name: tencent-docs-stable
slug: tencent-docs-stable
displayName: Tencent Docs Stable Channel
version: "1.0.0"
description: "Stable write path for Tencent Docs (腾讯文档) when the MCP connector fails. Trigger keywords: tencent docs upload failed, tdoc_init no_token, 无token, 文档推送失败, connector timeout, insert paragraph failed, smart document push, 腾讯文档初始化失败. Covers failure classification, token self-check, direct JSON-RPC fallback via runtime-resolved gateway config, 16KB content chunking, and a quick reference for the most-used document tools."
description_zh: "腾讯文档写入通道稳定层：MCP 连接器失败（no_token / 超时 / 初始化失败）时的故障分类、token 自检、JSON-RPC 直连兜底（endpoint 运行时从配置解析，不硬编码），长内容 16KB 分块追加，常用文档工具速查。"
read_when: "Any Tencent Docs write/upload fails, or before pushing content longer than ~8KB through the connector."
not_for:
  - Reading local Office files (use tencent-local-office-edit / format-extract)
  - Converting HTML to DOCX (use html-to-docx)
  - Notion or other document platforms
  - Publishing to WeChat Official Account
agent_created: true
---

# Tencent Docs Stable Channel

Purpose: make Tencent Docs writes survive connector failures. The MCP connector is the primary path; this skill defines a deterministic self-check sequence and a direct JSON-RPC fallback so a broken connector does not kill the task.

## Workflow

1. [Deterministic] **Classify the failure** from the error message: `no_token` / auth error / timeout / validation error / rate limit. Each class has a different remediation (see Failure Handling).
2. [Deterministic] **Token self-check**: resolve credentials the way the connector documents them (config file, environment variable, or connector-managed store). Verify the source exists and is non-empty. Never print the token value.
3. [Deterministic] **Retry once** after ~5 seconds with the identical call. Gateway blips are the most common transient cause; one retry resolves most of them.
4. [LLM] **Direct JSON-RPC fallback** (only if the connector still fails after one retry):
   - Read the MCP server configuration at runtime to discover the gateway endpoint. Never hardcode host, port, or token in scripts or skill text.
   - Send JSON-RPC requests over HTTP: method = tool name, params = tool arguments, same schema the connector uses.
   - For content above ~16KB, split at paragraph boundaries into chunks of ≤16KB and append sequentially (create document → append chunk 1 → append chunk 2 → ...). Appending whole documents in one call silently truncates or fails validation.
5. [Deterministic] **Verify**: read back the document title and first paragraph; confirm appended chunk count equals the number sent. Report the document link.

## Hard Rules

- Never print, log, or embed the token value anywhere.
- Never hardcode gateway endpoint, port, or credentials — always resolve from config at runtime.
- Exactly one retry before switching to fallback. No retry loops.
- Chunk size ≤16KB, split at paragraph boundaries, never mid-paragraph.
- After fallback, always verify by reading back, not by trusting the write response alone.

## Failure Handling

- `no_token` after self-check: report which credential source was checked and what was missing; stop. Do not invent a token.
- Both connector and fallback fail (one attempt each): mark the target UNREACHABLE with both error messages as evidence; do not keep polling.
- Validation error on content: check for unsupported HTML constructs (tables, nested lists, images with external hosts); degrade to plain paragraphs and retry once.
- Rate limit: wait 60s, retry once, then stop.

## Output Format

Short report: failure class → remediation used (retry / fallback) → verification result (title, paragraph count, chunk count) → document link.
