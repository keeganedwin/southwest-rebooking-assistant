# Original Prompt Notes (verbatim)

These are the notes exactly as originally written, with spelling and wording unchanged. A cleaned-up version is in [PROMPT_NOTES.md](PROMPT_NOTES.md).

## First message

> My goal is to create an assistant that's going to help agents of the SouthWest Airlines rebook passengers. Please follow all the following instructions:
>
> * Use the Storm Travel Waiver Fare Rules Bulletin (Denver, November 2026) as reference
>
> What the assistant needs to do:
>
> * Rebooked flights must depart between 18 November 2026 and 23 November 2026
> * Assistant needs to flag these out-of-scope requests
>
> How the assistant needs to split larger groups, especially in case of families:
>
> * I need a REBOOKING_SPEC.md which should be 2 pages long as I would want to cover an edge-case of any new employee/persons going through the instructions
> * It should also contain all the suggestions, agent confirmations, split parties, fare rules
>
> * Split by ages or adult with each child
> * And always first look for a flight with room for everyone
>
> Agent again checks before passing suggestions to customers
>
> Also aid me in putting REBOOKING_SPEC.md at the root of a new Github repo along with more refined version of my prompt notes

## Follow-up decisions

> * age cut-offs: make it so that any child under the legal age of 18 needs at least on adult. A group of 4 adults can be split however.
> * Two different date ranges are in play. The bulletin (1.1) covers bookings whose original flight was cancelled between 15 and 18 November 2026, and the rebooked flight must depart between 18 and 23 November 2026 (2.1). The waiver stays in force through 23 November (1.5). The 18 to 23 window is only for the new flight. Put both ranges in REBOOKING_SPEC.md so a new reader doesn't mix them up
> * Regarding the fares, the assitant indeed does not promise any refund and should suggest and leave the decision up to the agent
> * I am currently not provided with any 'flight_seat_availability' document/data.
> * You can retain the suggested name of the repo but I want it to be a public repo
> * Show me all the suggested changes before giving me the option to push it to the repo

## Follow-up on splitting

> * For the rule of line 57 'by age', change it to even if there are similar age siblings, as long as they're under 18, they always need to be accompanied by one adult if split. But in the event that they are travelling in a group of 3 or 3, and there's only one adult, then they can never be split and have to all be on the same flight.
> * Length - as long as it more or less splits past two pages, that's okay.
