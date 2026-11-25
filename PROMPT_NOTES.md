# Prompt Notes: Storm Rebooking Assistant

A cleaned-up version of the original notes. Decisions already made are listed in Section 6, and what is still open is in Section 7.

## 1. Goal

Build an assistant that helps Southwest Airlines contact-center agents rebook passengers whose flights were cancelled by the November 2026 Denver snowstorm. The assistant suggests; the agent decides and confirms with the customer.

## 2. Reference material

- *Storm Travel Waiver Fare Rules Bulletin (Denver, November 2026)*, the source of truth for rules and fares. Do not contradict it.
- 60 logged storm calls (CL-001 to CL-060), used to see where agents lose time: finding flights, reseating parties, and checking waiver rules.
- One senior-agent interview, used for how the work is actually done today.

## 3. What the assistant must do

| Requirement | Detail |
|---|---|
| Suggest only valid flights | New flight departs 18 to 23 Nov 2026 inclusive; same airports; not cancelled; not earlier than the customer's earliest acceptable departure |
| Keep the two date ranges apart | **15 to 18 Nov** is when the *original* flight was cancelled (clause 1.1). **18 to 23 Nov** is when the *new* flight departs (clause 2.1). The waiver stays in force through 23 Nov (clause 1.5) |
| Flag out-of-scope requests | Outside the date window, alternate airports, or made after 23 Nov: show "OUT OF SCOPE" with the clause, offer no flights, route to Revenue Management |
| Look first for room for everyone | Always search for one flight that fits the whole party before considering any split |
| Apply fare rules exactly | Waiver eligible: no fare difference either way. Not eligible: fare difference only when the new fare is higher, shown as an estimate. The assistant never promises a refund; the agent decides |
| Keep the agent in control | The agent checks every suggestion, then confirms with the customer. The assistant never books or contacts customers |

## 4. How to split larger parties (especially families)

Only when no flight has room for everyone:

1. **Minors travel with an adult.** Anyone under 18 needs at least one adult (18 or over) from their party on the same flight.
2. **A party of adults only** (for example four adults) can be split any way.
3. **Siblings do not count as adults.** Even similar-age siblings, as long as all are under 18, always need an adult if the party is split.
4. **One adult with minors: never split.** The whole party stays on the same flight (a later one if needed), or goes to a supervisor.
5. Aim for two flights, close together.
6. Nobody is charged because of a split. Each flight must meet the scope rules.

## 5. Deliverables

- `REBOOKING_SPEC.md` at the repository root, about two printed pages, written so a new employee can follow it without help. It covers suggestions (with an example), agent confirmation, split parties, fare rules, both date ranges, and a short glossary.
- This file, `PROMPT_NOTES.md`, and `ORIGINAL_PROMPT_NOTES.md`, which holds the original notes word for word.
- `README.md`, a short project overview.
- All of them live at the root of a new **public** GitHub repository named `southwest-rebooking-assistant`.

## 6. Decisions made

1. **Age:** anyone under 18 is a minor and needs at least one adult from their party. Adults-only parties can be split freely.
2. **Dates:** 15 to 18 Nov covers the original flight's cancellation; 18 to 23 Nov covers the new flight's departure only. Both are stated in the spec so they are not confused.
3. **Refunds:** the assistant never promises one. It suggests, and the agent decides.
4. **Agent check:** reviewing every suggestion before it reaches the customer is mandatory.
5. **Repository:** public, named `southwest-rebooking-assistant`.

## 7. Still open

- **Seat data:** no `flight_seat_availability` data has been provided. Until it is, the assistant treats seat adjacency as unknown and the agent confirms seats together.

## 8. Ready-to-paste prompt

> You are helping design an assistant for Southwest Airlines contact-center agents who rebook customers after the November 2026 Denver snowstorm. Use the attached *Storm Travel Waiver Fare Rules Bulletin* as the only source of rules.
>
> Two date ranges apply. The original flight must have been cancelled between 15 and 18 November 2026 (clause 1.1). The new flight must depart between 18 and 23 November 2026 inclusive (clause 2.1), on the original airport pair, not cancelled, and not earlier than the customer's earliest acceptable departure. The waiver stays in force through 23 November (clause 1.5). Flag requests outside these limits as out of scope and route them to Revenue Management without offering flights.
>
> Always look first for one flight with room for the whole party. Only if none exists may you split the party. Anyone under 18 must have at least one adult from their party on the same flight, and siblings under 18 never count as adults. Adults-only parties can be split any way. A party with only one adult and any minors can never be split and must stay on one flight. Keep the number of flights to a minimum.
>
> Apply the bulletin's fare rules exactly: no fare difference for waiver-eligible bookings, and the original fare-class rules for the rest. Never promise a refund; suggest, and leave the decision to the agent. The assistant never books or contacts customers, and an agent reviews every suggestion before it reaches the customer.
>
> Before writing anything, list what is unclear and what you would assume. Then produce a two-page `REBOOKING_SPEC.md` written for a new employee, covering suggestions, agent confirmation, split parties, fare rules, and both date ranges.
