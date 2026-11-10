## Description:

Provides a recovery workflow for Tencent Docs write failures, including credential checks, bounded retries, chunked writes, and verification.

This skill is ready for commercial/non-commercial use.

## Publisher:

[haiyangchenbj](https://clawhub.ai/user/haiyangchenbj)

### License/Terms of Use:

MIT-0

## Use Case:

Developers and agents use this skill to recover Tencent Docs write, upload, and push operations when connector errors, timeouts, or long-content truncation occur, while verifying updates and preserving existing documents.

### Deployment Geography for Use:

Global

## Known Risks and Mitigations:

Risk: The malformed version field in the skill artifact may prevent installation.

Mitigation: Correct and confirm the version field before installation; the server-resolved release version is 1.0.3.

Risk: Using an unintended credential source or untrusted gateway could expose Tencent Docs access.

Mitigation: Use only the intended Tencent Docs credential source and trusted configured gateway, and never print or log the token.

Risk: Timeout retries may leave duplicate content in a document.

Mitigation: Check recovered documents for duplicate content after timeout retries.

## Reference(s):


## Skill Output:

**Output Type(s):** [Guidance, Text, Markdown]

**Output Format:** [Short Markdown report]

**Output Parameters:** [1D]

**Other Properties Related to Output:** [Reports the failure class, remediation, verification result, and document link.]

## Skill Version(s):

1.0.3 (source: server-resolved release metadata)

## Ethical Considerations:

Users should evaluate whether this skill is appropriate for their environment, review any generated or modified files before relying on them, and apply their organization's safety, security, and compliance requirements before deployment.
