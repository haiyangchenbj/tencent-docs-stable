## Description:

Stable write path for Tencent Docs when the MCP connector fails, covering failure classification, credential self-checks, one retry, JSON-RPC fallback, paragraph-boundary chunking, and read-back verification.

This skill is ready for commercial/non-commercial use.

## Publisher:

[haiyangchenbj](https://clawhub.ai/user/haiyangchenbj)

### License/Terms of Use:

MIT-0

## Use Case:

Developers and agent operators use this skill when Tencent Docs write, upload, push, or paragraph-insert operations fail, time out, report missing credentials, or risk truncating long content. It guides the agent through bounded retries, direct fallback writes, chunked appends, and verification before reporting the document link.

### Deployment Geography for Use:

Global

## Known Risks and Mitigations:

Risk: The skill may access existing Tencent Docs connector credentials and write content to Tencent Docs.

Mitigation: Install and use it only for Tencent Docs accounts and documents the agent is intended to modify; never print or embed token values.

Risk: Long Tencent Docs writes can truncate or fail validation when sent as a single operation.

Mitigation: Split content into chunks of 16KB or less at paragraph boundaries, append sequentially, and verify the written document by reading it back.

Risk: Repeated connector retries or polling can amplify transient failures or rate limits.

Mitigation: Retry once with the same call, then use the direct fallback once; for rate limits, wait 60 seconds, retry once, and stop if it still fails.

## Reference(s):

- [ClawHub skill page](https://clawhub.ai/haiyangchenbj/skills/tencent-docs-stable)

## Skill Output:

**Output Type(s):** [guidance, shell commands, configuration, markdown]

**Output Format:** [Markdown short report with failure class, remediation used, verification result, and document link]

**Output Parameters:** [1D]

**Other Properties Related to Output:** [May include title, paragraph count, chunk count, and unreachable status when both connector and fallback fail.]

## Skill Version(s):

1.0.2 (source: frontmatter and server release evidence)

## Ethical Considerations:

Users should evaluate whether this skill is appropriate for their environment, review any generated or modified files before relying on them, and apply their organization's safety, security, and compliance requirements before deployment.
