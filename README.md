# Southwest Storm Rebooking Assistant

A specification for an assistant that helps contact-center agents rebook customers whose flights were cancelled by the November 2026 Denver snowstorm. The assistant **suggests**; the agent decides and confirms with the customer. It never books, charges or contacts customers.

## Read this first

| File | What it is |
|---|---|
| [REBOOKING_SPEC.md](REBOOKING_SPEC.md) | The full specification (about two pages), written so a new employee can follow it without help |
| [PROMPT_NOTES.md](PROMPT_NOTES.md) | The refined brief behind the spec: decisions made, open questions, and a ready-to-paste prompt |

Rules come from the *Storm Travel Waiver Fare Rules Bulletin (Denver, November 2026)*.

## The spec in brief

**Five golden rules**
1. The assistant never books, charges or contacts a customer. Only the agent does.
2. It always looks first for one flight with room for everyone.
3. Every suggested flight must pass the scope checks.
4. Anything out of scope is flagged, not worked around.
5. If data is missing or unclear, it says so. It never guesses.

**Two date ranges (do not mix them up)**
- **15 to 18 Nov 2026:** the *original* flight was cancelled (clause 1.1).
- **18 to 23 Nov 2026, inclusive:** the *new* flight departs (clause 2.1). The waiver stays in force through 23 Nov (clause 1.5).
- Requests outside these limits are flagged **OUT OF SCOPE** and sent to Revenue Management for manual review.

**Splitting a party (only if no flight has room for everyone)**
- Anyone under 18 needs at least one adult from their party on the same flight. Siblings do not count as adults.
- A party with one adult and any minors is never split.
- A party of adults only can be split any way.

**Fare rules**
- Waiver eligible: no fare difference in either direction.
- Not eligible: fare difference only if the new fare is higher, shown as an estimate. The assistant never promises a refund; the agent decides.

**Agent confirmation:** the agent checks every suggestion, then gets the customer's yes, before anything is booked.

## Status

Draft v0.2. One open question: seat availability data has not been provided yet, so seat adjacency is treated as unknown.
