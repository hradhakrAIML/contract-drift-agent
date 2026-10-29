# User guide for Finance reviewers

## What the agent does
It reads each signed contract PDF, compares it to the approved deal in the system, and sorts it:

- **Auto-approved**: every check passed. No action needed.
- **Rejected**: the document does not match the approved deal (wrong partner, deal type, rate card type, value off by more than 1%, or no deal on file). Fix the contract or the deal and resubmit.
- **Review**: something needs your judgment. It appears under **Needs review** with the reasons.

## Contract drift
Drift means the signed contract says something the system does not. The most common cause is a Legal amendment after approval that never made it into the system. Examples:

- Post-termination obligations changed from 0 months to 2 years
- Bounty now payable only if the partner applies security patches within 30 days
- A rate changed, or a country or device tier was added or removed from the rate card

Each drift flag shows the system value, the contract value, and the exact clause it came from.

## Working a flagged contract
1. Open **Needs review** or **Contract drift** and select a contract.
2. Read **Why it was flagged**. Compare the system and contract values and the source clause.
3. Check the rate card table; changed cells are highlighted.
4. Enter your name and a short note. The note is part of the audit record.
5. Approve or reject. For drift, leave **Request system record update** checked if the contract is correct and the system is stale.

## When not to rely on it
- Scanned or image-only PDFs: these always come to you as unreadable
- Deal types or clauses added after the last eval run
- Any contract where the business context matters more than the terms

If the agent gets something wrong, report the run ID shown under the partner name. Misses become new eval cases.
