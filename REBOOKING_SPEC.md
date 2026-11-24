# Storm Rebooking Assistant: Specification

**Version 0.1 (draft)** · Rules source: *Storm Travel Waiver Fare Rules Bulletin (Denver, November 2026)*, issued 15 Nov 2026 · Evidence: 60 logged storm calls and one senior-agent interview

## 1. Purpose and five golden rules

The assistant helps contact-center agents rebook customers whose flights were cancelled by the Denver snowstorm. It **suggests**. The agent decides, and the customer says yes. If you are new, read this section and Section 7, then use the rest as a reference.

1. The assistant never books, charges or contacts a customer. Only the agent does.
2. It **always looks first for a flight with room for everyone**.
3. Every suggested flight must pass the scope checks in Section 3.
4. Anything out of scope is **flagged**, not worked around.
5. If data is missing or unclear, it says so. It never guesses.

## 2. What the assistant needs to know

- **Booking:** `waiver_eligible`, `fare_class`, `fare_paid_usd`, `earliest_acceptable_departure`, origin, destination, onward connection, and each traveller's age category (adult or child).
- **Flights:** `flight_status`, `departure_datetime`, `fare_class_open`, open seats, and whether the open seats are together.
- Age category is not in the bulletin or the logs. It must come from the booking, or the agent is asked. Do not assume it.

## 3. Scope checks (every suggested flight must pass all)

| # | Check | Clause | If it fails |
|---|---|---|---|
| 1 | Original flight cancelled 15 to 18 Nov 2026 at an affected station (DEN, MDW, LAS, PHX, BWI, HOU, DAL, LAX, OAK, SAN, STL, MCO, BNA, SMF) | 1.1, 1.2 | Flag |
| 2 | New flight departs **18 to 23 Nov 2026 inclusive** | 2.1 | Flag |
| 3 | Same origin and destination airports | 2.2 | Flag |
| 4 | `flight_status` is not cancelled (scheduled and delayed are fine) | 2.3 | Do not suggest |
| 5 | Departs at or after `earliest_acceptable_departure` | 2.4 | Do not suggest |
| 6 | Request made on or before 23 Nov 2026 | 1.5, 2.5 | Flag |
| 7 | Arrives in time for any onward connection | agent interview | Do not suggest |

**Out-of-scope flag.** For checks 1, 2, 3 and 6 the assistant shows **OUT OF SCOPE: send to Revenue Management for manual review**, names the failed clause, and offers no flights. The agent tells the customer it is under review. Alternate-airport requests and travel outside 18 to 23 Nov are always flagged.

*Decision to confirm:* the bulletin applies the 18 to 23 Nov window (2.1) to waiver bookings and lists only 2.2 to 2.4 for non-waiver bookings (4.3). This spec applies the window to every rebooking, which is the safer reading.

## 4. Finding a flight

1. Search every flight that passes Section 3, so the agent only ever sees available flights.
2. **First look for one flight with room for the whole party.** Rank: (a) everyone on one flight, seated together; (b) everyone on one flight, not all seated together (show who is apart); (c) only if no flight has room for everyone, a split (Section 5).
3. Show at most three options, earliest departure first within a rank. Each shows flight, times, seats, fare treatment and why it ranks there.

## 5. Splitting a party (families especially)

**When:** only if no valid flight has room for everyone (clause 3.5).

1. **An adult with each child.** Every flight in a split carries at least one adult for each child on it. A child is never on a flight without an adult from their party.
2. **By age.** Within that rule, keep the youngest children with the adult they travel with, and keep similar-age siblings together.
3. **Fewest flights, smallest gap.** Aim for two flights, as close together as possible.
4. **No safe split? Do not split.** For example, one adult with three children. Offer the whole party on a later flight, or escalate to a supervisor.
5. **Customer refuses a split:** escalate to a supervisor.
6. Every flight in the split must pass Section 3. Nobody pays a fee, penalty or fare difference because of the split (3.5).
7. Document each booking with the waiver line (Section 6) and cross-reference the other booking numbers. The agent reads both back.

*Example:* a family of two adults and two children, with only three seats together on the evening flight. Suggest evening: adult plus two children; next morning: second adult. Or, if two seats exist on each flight, adult plus child on each. The agent picks.

*Age cut-offs (what counts as a child or a minor) are an open question. See Section 9.*

## 6. Fare rules

| | Waiver eligible (`true`) | Not eligible (`false`) |
|---|---|---|
| Fare difference | **None, in either direction** (3.1) | Collected **only if the new open fare is higher** (4.1) |
| Fare paid | Carries over unchanged (3.2) | Original fare class rules apply |
| Fare class of new flight | May differ; customer keeps original benefits (3.3) | Fare class decides (table below) |
| Change fees | None (3.4) | Per original fare class |
| Split party | No penalty to anyone (3.5) | Same rules for each member |
| Documentation | `STORM WAIVER DEN NOV26` + original confirmation number, on every rebooked flight (3.6) | Not recorded (4.4) |

- The flag decides eligibility. **Agents and the assistant do not re-assess it** (1.4).
- Wanna Get Away, Wanna Get Away Plus, Anytime and Business Select can all change. Anytime and Business Select are refundable per standard rules. Loyalty tier changes nothing (4.2).
- For non-eligible customers the assistant shows the fare difference as an **estimate for the agent to confirm** and **never auto-rebooks at a charge**.

## 7. Agent confirmation: nothing reaches the customer unchecked

1. The assistant presents ranked options and any flags.
2. **The agent checks:** flag matches the booking; departs 18 to 23 Nov; same airports; not cancelled; not earlier than the earliest acceptable time; connection works; every child is with an adult; fare statement is correct.
3. The agent accepts, edits or rejects each option, and the assistant records the reason.
4. The agent gives the customer the choice and **gets the customer's yes**.
5. The agent books. The assistant drafts the note and the agent reviews it before saving.

**Who to escalate to**

| Situation | Escalate to |
|---|---|
| Out of scope (Section 3) | Revenue Management |
| Seat moves, exceptions | Floor lead |
| Fare dispute, refund request, split refused | Supervisor |

## 8. Worked checks

- **Eligible couple, 19 Nov, same airports:** two seats together found, $0 difference, waiver line recorded.
- **Not eligible, solo:** flight found, the new fare is $40 higher, so show an estimated $40 for agent confirmation. Do not rebook automatically.
- **Request for a 24 Nov departure:** flagged out of scope, sent to Revenue Management, no options offered.

## 9. Open questions (need an owner)

1. What age counts as a child, and what as a minor who cannot travel alone? The bulletin and logs are silent.
2. Confirm the 18 to 23 Nov window applies to non-waiver bookings (Section 3).
3. When the new fare is lower for a non-eligible customer, is anything refunded? The bulletin is silent.
4. Where does seat adjacency data come from, and how fresh is it? (Seat screens refreshed mid-search in several logged calls.)
