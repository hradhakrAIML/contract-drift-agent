# Governance

## Data classification

| Data | Class | Handling |
|---|---|---|
| Contract text | Confidential | Redacted before the model; never stored raw |
| Personal data (email, phone, account numbers, SSN) | Restricted | Replaced with tokens like `[EMAIL]` before any model call or log |
| Extracted terms | Confidential | Stored in the audit log for decision traceability |
| Deal records | Confidential | Read-only from the system of record |

## Controls

- **Redaction first.** Redaction runs before the prompt is built. Only counts of redactions are logged, never values.
- **No raw documents at rest.** The audit log stores a SHA-256 hash of each document, so a reviewer can prove which file was decided without the service keeping a copy.
- **Model cannot approve.** Approval comes from deterministic rules. Any schema failure, low confidence, or budget breach escalates to a person.
- **Append-only audit log.** Each run records decision, rules fired, extracted terms, model, prompt version, tokens, cost, and latency. Reviewer resolutions record who, what, when, and why.
- **Least privilege.** The Cloud Run service is private, runs as a dedicated service account with Vertex AI access only, and requires `roles/run.invoker` to call.
- **Vendor data use.** On Vertex AI, prompts stay in the customer's GCP project under Google Cloud data terms. Confirm with Security before switching providers.

## Retention

Set by policy, not code. Recommended default: audit log kept 7 years for SOX-relevant approvals; nothing else persisted.

## Open items

- Role-based auth on reviewer endpoints (identity-aware proxy or OIDC)
- Separation of duties: block a reviewer from resolving a contract they submitted
