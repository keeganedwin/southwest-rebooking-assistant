# Southwest Storm Rebooking Assistant

A product specification for an assistant that helps contact-center agents rebook passengers whose flights were cancelled by the November 2026 Denver snowstorm. The assistant suggests options; the agent reviews them and confirms with the customer.

**Status:** specification stage. No code yet.

## The problem

Rebooking during a storm is slow, manual work. Agents lose time in three places: finding open flights, reseating larger parties, and checking fare waiver rules.

## Key design rules

| Area | Rule |
|---|---|
| Human in control | The assistant never books, charges or contacts a customer. An agent checks every suggestion. |
| Flight search | It looks first for one flight with room for the whole party, and splits the party only if none exists. |
| Travel dates | The original flight was cancelled 15 to 18 Nov 2026. The new flight departs 18 to 23 Nov 2026. |
| Out of scope | Requests outside the rules are flagged and sent to Revenue Management for manual review. |
| Families | Anyone under 18 travels with an adult from their party. One adult with minors is never split. |
| Fares | Waiver-eligible bookings pay no fare difference. Others pay only if the new fare is higher. No refund is ever promised. |

## Documentation

| File | Contents |
|---|---|
| [REBOOKING_SPEC.md](REBOOKING_SPEC.md) | **Start here.** The full specification. |
| [PROMPT_NOTES.md](PROMPT_NOTES.md) | Requirements, decisions made and the prompt. |
| [ORIGINAL_PROMPT_NOTES.md](ORIGINAL_PROMPT_NOTES.md) | The original notes, word for word. |

## Open item

Seat availability data has not been provided yet, so the specification treats seat adjacency as unknown and has the agent confirm it.

---

Rules source: *Storm Travel Waiver Fare Rules Bulletin (Denver, November 2026)*, issued 15 Nov 2026.
