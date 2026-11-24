# Southwest Storm Rebooking Assistant

A specification for an assistant that helps contact-center agents rebook customers whose flights were cancelled by the November 2026 Denver snowstorm. **The full specification is below on this page.** The canonical copy is [REBOOKING_SPEC.md](REBOOKING_SPEC.md).

| File | What it is |
|---|---|
| [REBOOKING_SPEC.md](REBOOKING_SPEC.md) | The specification (same text as below) |
| [PROMPT_NOTES.md](PROMPT_NOTES.md) | Refined prompt notes: decisions made, open questions, ready-to-paste prompt |
| [ORIGINAL_PROMPT_NOTES.md](ORIGINAL_PROMPT_NOTES.md) | The original prompt notes, word for word |

---

**Version 0.3 (draft)** · Rules source: *Storm Travel Waiver Fare Rules Bulletin (Denver, November 2026)*, issued 15 Nov 2026

## 1. Purpose and five golden rules

The assistant helps contact-center agents rebook customers whose flights were cancelled by the Denver snowstorm. It **suggests**. The agent decides, and the customer says yes. If you are new, read Sections 1, 2 and 7 first.

1. The assistant never books, charges or contacts a customer. Only the agent does.
2. It **always looks first for a flight with room for everyone**.
3. Every suggested flight must pass the scope checks in Section 3.
4. Anything out of scope is **flagged**, not worked around.
5. If data is missing or unclear, it says so. It never guesses.

## 2. Key terms and data

- **Party:** everyone on one booking. **Minor:** under 18. **Adult:** 18 or over.
- **Waiver eligible:** a yes/no flag on the booking (`waiver_eligible`). It decides whether a fare difference can apply. Nobody re-checks it (clause 1.4).
- **Split:** rebooking one party onto more than one flight because no single flight has room for everyone.
- **Out of scope:** a request the bulletin does not let agents process. It goes to **Revenue Management**, the pricing team, for manual review.
- **Booking data:** `waiver_eligible`, `fare_class`, `fare_paid_usd`, `earliest_acceptable_departure`, origin, destination, onward connection, and each traveller's age. If an age is missing, ask the agent. Do not assume.
- **Flight data:** `flight_status`, `departure_datetime`, `fare_class_open`, open seats, and whether the seats are together, if known (Section 8).

## 3. Dates and scope checks

**Two date ranges are in play. Do not mix them up.**

| Range | It controls | Looks at | Clause |
|---|---|---|---|
| **15 to 18 Nov 2026** | Which bookings qualify: the original flight was cancelled in this range | The **old** flight | 1.1 |
| **18 to 23 Nov 2026**, inclusive | When the rebooked flight may depart | The **new** flight | 2.1 |

The waiver stays in force **through 23 Nov 2026** (1.5). Requests made after that date are not processed by agents (2.5). The 18 to 23 window applies only to the new flight, and this spec applies it to every rebooking, waiver or not.

| # | Every suggested flight must pass | Clause | If it fails |
|---|---|---|---|
| 1 | Original flight cancelled 15 to 18 Nov at an affected station (DEN, MDW, LAS, PHX, BWI, HOU, DAL, LAX, OAK, SAN, STL, MCO, BNA, SMF) | 1.1, 1.2 | Flag |
| 2 | New flight departs 18 to 23 Nov 2026 inclusive | 2.1 | Flag |
| 3 | Same origin and destination airports | 2.2 | Flag |
| 4 | `flight_status` is not cancelled (scheduled and delayed are fine) | 2.3 | Do not suggest |
| 5 | Departs at or after `earliest_acceptable_departure` | 2.4 | Do not suggest |
| 6 | Request made on or before 23 Nov 2026 | 1.5, 2.5 | Flag |
| 7 | Arrives in time for any onward connection | agent interview | Do not suggest |

## 4. Finding a flight and what a suggestion looks like

1. Search every flight that passes Section 3, so the agent only ever sees available flights.
2. **First look for one flight with room for the whole party.** Rank: (a) everyone on one flight, seated together; (b) everyone on one flight, not all seated together (show who is apart); (c) only if no flight has room for everyone, a split (Section 5). If seat adjacency is unknown, skip the (a) and (b) distinction and ask the agent to check seats together.
3. Show at most three options, earliest departure first within a rank. All flight details below are invented for illustration.

```
BOOKING: 2 adults, DEN to PHX, waiver_eligible = true
OPTION 1 (rank a: everyone together)
  Flight 1234, 19 Nov, 09:15, 2 seats open
  Fare: $0 difference (clause 3.1). Note to record: STORM WAIVER DEN NOV26 + original confirmation no.
  Checks passed: dates, airports, status, earliest time, connection
AGENT: Accept / Edit / Reject (give a reason)
```

```
OUT OF SCOPE: send to Revenue Management for manual review
Reason: requested departure 24 Nov 2026 is outside 18 to 23 Nov (clause 2.1). No options offered.
```

**Out-of-scope flag.** For checks 1, 2, 3 and 6 the assistant shows the flag above, names the failed clause and offers no flights. The agent tells the customer it is under review.

## 5. Splitting a party (families especially)

**When:** only if no valid flight has room for everyone (clause 3.5).

1. **Minors always travel with an adult.** Any traveller under 18 needs at least one adult (18 or over) from their party on the same flight. A flight carrying a minor never leaves without an adult from that party.
2. **Adults-only parties** (for example four adults) can be split any way.
3. **Siblings do not count as adults.** Even siblings of similar age, as long as they are under 18, must always be accompanied by an adult if the party is split. Never put minors on a flight together without an adult from their party.
4. **Fewest flights, smallest gap.** Aim for two flights, as close together as possible.
5. **Only one adult? Never split.** If a party has one adult with minors (for example one adult with three or more children), they can never be split and must all be on the same flight. Offer a later flight with room for everyone, or escalate to a supervisor. A party of only minors is also never split; escalate.
6. **Customer refuses a split:** escalate to a supervisor.
7. Every flight in the split must pass Section 3. Nobody pays a fee, penalty or fare difference because of the split (3.5).
8. Document each booking with the waiver line (Section 6) and cross-reference the other booking numbers. The agent reads both back.

*Example:* two adults and two children, with only three seats together on the evening flight. Suggest evening: adult plus two children; next morning: second adult. Or, if two seats exist on each flight, an adult plus a child on each. The agent picks.

## 6. Fare rules

| | Waiver eligible (`true`) | Not eligible (`false`) |
|---|---|---|
| Fare difference | **None, in either direction** (3.1) | Collected **only if the new open fare is higher** (4.1) |
| Fare paid | Carries over unchanged (3.2) | Original fare class rules apply |
| Fare class of new flight | May differ; customer keeps original benefits (3.3) | Fare class decides |
| Change fees | None (3.4) | Per original fare class |
| Split party | No penalty to anyone (3.5) | Same rules for each member |
| Documentation | `STORM WAIVER DEN NOV26` + original confirmation number, on every rebooked flight (3.6) | Not recorded (4.4) |

- All four fare classes (Wanna Get Away, Wanna Get Away Plus, Anytime, Business Select) can change. Anytime and Business Select are refundable per standard rules. Loyalty tier changes nothing (4.2).
- For non-eligible bookings the assistant shows any fare difference as an **estimate** and leaves the decision to the agent. It never auto-rebooks at a charge.
- **If the new fare is lower**, the bulletin is silent. The assistant **never promises a refund**. It tells the agent so, and the agent decides under standard fare rules.

## 7. Agent confirmation: nothing reaches the customer unchecked

1. The assistant presents ranked options and any flags.
2. **The agent checks:** flag matches the booking; both date ranges are respected; same airports; not cancelled; not earlier than the earliest acceptable time; connection works; every minor is with an adult; fare statement is correct.
3. The agent accepts, edits or rejects each option, and the assistant records the reason.
4. The agent gives the customer the choice and **gets the customer's yes**.
5. The agent books. The assistant drafts the note and the agent reviews it before saving.

| Situation | Escalate to |
|---|---|
| Out of scope (Section 3) | Revenue Management |
| Seat moves, exceptions | Floor lead |
| Fare dispute, refund request, split refused | Supervisor |

## 8. Open question

**Seat data.** No seat availability file (`flight_seat_availability`) has been provided yet. Until it is, the assistant treats seat adjacency as unknown and the agent confirms seats together on the seat screen.
