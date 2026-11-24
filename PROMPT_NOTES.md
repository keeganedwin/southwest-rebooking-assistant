# Prompt Notes: Storm Rebooking Assistant

A cleaned-up version of the original notes. Ambiguities are resolved explicitly, and every assumption is listed so it can be corrected.

## 1. Goal

Build an assistant that helps Southwest Airlines contact-center agents rebook passengers whose flights were cancelled by the November 2026 Denver snowstorm. The assistant suggests; the agent decides and confirms with the customer.

## 2. Reference material

- *Storm Travel Waiver Fare Rules Bulletin (Denver, November 2026)*, the source of truth for rules and fares. Do not contradict it.
- 60 logged storm calls (CL-001 to CL-060), used to see where agents lose time: finding flights, reseating parties, and checking waiver rules.
- One senior-agent interview, used for how the work is actually done today.

## 3. What the assistant must do

| Requirement | Detail |
|---|---|
| Suggest only valid flights | Departs 18 to 23 Nov 2026 inclusive; same airports; not cancelled; not earlier than the customer's earliest acceptable departure |
| Flag out-of-scope requests | Outside the date window, alternate airports, or made after 23 Nov: show "OUT OF SCOPE" with the clause, offer no flights, route to Revenue Management |
| Look first for room for everyone | Always search for one flight that fits the whole party before considering any split |
| Apply fare rules exactly | Waiver eligible: no fare difference either way. Not eligible: fare difference only when the new fare is higher, shown as an estimate for the agent |
| Keep the agent in control | The agent checks every suggestion, then confirms with the customer. The assistant never books or contacts customers |

## 4. How to split larger parties (especially families)

Only when no flight has room for everyone:

1. **An adult with each child.** A child is never on a flight without an adult from their party.
2. **By age.** Keep the youngest children with their adult, and similar-age siblings together.
3. Aim for two flights, close together. If no safe split exists, do not split; offer a later flight or escalate.
4. Nobody is charged because of a split. Each flight must meet the scope rules.

## 5. Deliverables

- `REBOOKING_SPEC.md` at the repository root, about two printed pages, written so a new employee can follow it without help. It covers suggestions, agent confirmation, split parties and fare rules.
- This file, `PROMPT_NOTES.md`.
- Both live at the root of a new GitHub repository.

## 6. Assumptions I made (please correct)

1. "Split by ages or adult with each child" is read as: pair each child with an adult first, then use age to decide who stays together. Age cut-offs are not defined anywhere yet.
2. The 18 to 23 Nov window applies to every rebooking. The bulletin states it for waiver bookings and is silent for others (clause 4.3).
3. "Agent checks again before passing suggestions to customers" is read as a mandatory review step, not an optional one.
4. The repository should be **private**, because it summarizes an internal fare-rules bulletin and call-center operations.

## 7. Ready-to-paste prompt

> You are helping design an assistant for Southwest Airlines contact-center agents who rebook customers after the November 2026 Denver snowstorm. Use the attached *Storm Travel Waiver Fare Rules Bulletin* as the only source of rules.
>
> The assistant must suggest only flights departing 18 to 23 November 2026 inclusive, on the original airport pair, not cancelled, and not earlier than the customer's earliest acceptable departure. It must flag requests outside these limits as out of scope and route them to Revenue Management without offering flights.
>
> It must always look first for one flight with room for the whole party. Only if none exists may it split the party. In a split, every child stays with at least one adult from their party, similar-age siblings stay together, and the number of flights is kept to a minimum. Never split if no safe split exists.
>
> Apply the bulletin's fare rules exactly: no fare difference for waiver-eligible bookings, and the original fare-class rules for the rest. The assistant never books or contacts customers. An agent reviews every suggestion before it reaches the customer.
>
> Before writing anything, list what is unclear and what you would assume. Then produce a two-page `REBOOKING_SPEC.md` written for a new employee, covering suggestions, agent confirmation, split parties and fare rules.
