# Decisions and lessons

Tradeoffs and lessons from building and testing the system.

## 2026-10-28: Model extracts, rules decide
LLMs are good at reading messy documents and bad at being consistently right about money. The model only produces structured terms; approval logic is plain code that Finance can read and audit.

## 2026-10-28: Mock model for CI
CI runs a deterministic regex extractor so tests and evals need no API keys and cost nothing. Real model evals run on demand with `LLM_PROVIDER=gemini`. Tradeoff: CI proves the pipeline and gates, not model quality.

## 2026-10-28: Bug caught by tests, PII pattern order
The phone regex matched digits inside bank account numbers before the account pattern ran, so account numbers were labeled as phones. Redaction still happened, but the audit counts were wrong. Fixed by running the most specific patterns first. Lesson: test redaction counts, not just removal.

## 2026-10-28: SQLite for now
Keeps local runs and CI self-contained. On Cloud Run the file is ephemeral, so this is not durable. Next: move `deals`, `audit_log`, and `review_queue` to BigQuery or Cloud SQL.

## 2026-10-28: Drift escalates, it does not reject
A post-approval Legal amendment is usually legitimate. Rejecting it would block valid contracts. Drift goes to a reviewer with both values and the source clause, and the reviewer can request a system record update. Identity mismatches (wrong partner, deal type, rate card type, or value) still reject, because they mean the document does not belong to the deal.

## 2026-10-28: Amended value wins
When a contract contains an amendment, extraction reports the value in force after the amendment, not the original clause. The original clause is still in the PDF, so a naive extractor reads "0 months" and misses the drift entirely. The prompt states this rule and the eval set has three amendment cases to hold it.

## 2026-10-28: Evidence must exist in the document
The model returns the sentence each value came from. Code checks every quote against the redacted text. A quote that is not there is treated as a hallucination and escalates. Reviewers see the clause, so they trust the flag without opening the PDF.

## 2026-10-28: BD contact is matched in code
PII redaction removes emails before the model call, which also removed the BD owner. Rather than weaken redaction, the BD contact is read from the raw text with a pattern and compared in code. A test asserts the email never appears in the prompt.

## 2026-10-28: Bug caught by the batch run, shared SQLite connection
The first 500-contract batch with 8 worker threads crashed on concurrent reads of one SQLite connection. Unit tests were single threaded, so they never saw it. Fixed with a re-entrant lock around all store access. Lesson: run the real batch shape before calling it done.

## Next
- Run evals against Gemini and record real accuracy, cost, and latency here
- Add harder cases: OCR for scanned PDFs, clauses that contradict the header, multi-currency, multi-page rate cards
- Write approved system updates back to the system of record through its API, with a second approver
- Feed resolved reviews back into the eval set
- Tracing (Langfuse or OpenTelemetry) per pipeline step
