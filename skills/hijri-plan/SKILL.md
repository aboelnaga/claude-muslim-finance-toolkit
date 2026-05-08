---
name: hijri-plan
description: Use when the user wants hledger periodic transaction rules for Hijri-linked events (Ramadan, Eid al-Fitr, Eid al-Adha) for a given Gregorian year. Looks up Hijri-to-Gregorian dates and emits sadaqah, Eid sacrifices, and Eid gift budget rules.
user_invocable: true
arguments:
  - name: year
    description: "The Gregorian year to generate Hijri events for (e.g. 2030)"
    required: true
---

# Hijri Event Planner for hledger

You are generating hledger periodic transaction rules for Hijri-linked events in a specific Gregorian year.

## Setup discovery

Before generating rules, gather context (in priority order):

1. **Read `CLAUDE.md` if present** in the project root or hledger directory — it may define currency, account naming conventions, and family size.
2. **Run `hledger accounts`** to see whether charity, family, zakat, or gifts categories already exist. Prefer suggesting existing accounts over inventing new ones.
   ```bash
   hledger -f main.journal accounts | grep -iE "charity|sadaqah|zakat|ramadan|eid|gift|family"
   ```
3. **Ask the user** for any of the following that aren't already discoverable:
   - Currency (e.g. USD, EUR, EGP, SAR, MYR, etc.)
   - Pre-Ramadan / Ramadan / tail-of-Ramadan sadaqah amounts (daily or monthly totals)
   - Eid al-Fitr gift budget
   - Eid al-Adha gift budget
   - Udhiyah (sacrifice) amount for Eid al-Adha
   - Family size for zakat al-fitr (per-person amount × number of household members)

The amounts shown in the templates below are **suggested defaults** — the user should adjust them to match their family's budget and locale.

## Your task

1. **Look up the approximate Gregorian dates** for these Hijri events in the requested year:
   - Ramadan start (1 Ramadan)
   - Eid al-Fitr (1 Shawwal)
   - Eid al-Adha (10 Dhul Hijjah)

   Use web search if available. Otherwise calculate: Hijri dates shift ~10–12 days earlier each Gregorian year. Reference table:

   ```text
   Reference dates (verified as of 2026; re-verify before using):
   Ramadan:      2026 Feb 17 | 2027 Feb 7  | 2028 Jan 28 | 2029 Jan 17
   Eid al-Fitr:  2026 Mar 20 | 2027 Mar 9  | 2028 Feb 27 | 2029 Feb 15
   Eid al-Adha:  2026 May 27 | 2027 May 16 | 2028 May 5  | 2029 Apr 24
   ```

   Hijri dates shift ~10–12 days earlier each Gregorian year — re-verify before each year, especially for years past 2029, since astronomical sighting can shift the actual start by ±1 day.

2. **Generate periodic rules** following the patterns below. Account names are **suggested defaults** based on common conventions; rename to match the user's existing taxonomy (e.g., `Expenses:religious:sadaqah` instead of `Expenses:charity:ramadan-sadka`, or `Expenses:family:udhiyah` instead of `Expenses:family:od7ia`).

   > Note on `od7ia`: this is a transliteration of the Arabic *udhiyah* (the Eid al-Adha sacrifice). Some users prefer `udhiyah`, `qurbani`, or `sacrifice` in their account tree — pick whichever matches your existing accounts.

   > Note on `sadka`: the account name `charity:ramadan-sadka` uses `sadka`, a common transliteration of the Arabic *sadaqah* (voluntary charity). Users are welcome to rename to `sadaqah`, `charity:ramadan-sadaqah`, or any other taxonomy that matches their existing accounts.

   **Pre-Ramadan sadaqah** (month before Ramadan starts):
   ```hledger
   ~ monthly from YYYY/MM/01 to YYYY/MM+1/01  ; Pre-Ramadan sadaqah buildup
       Expenses:charity:ramadan-sadka          USD     150.00
       Assets:bank:<account>
   ```

   **Ramadan month** (month containing Ramadan start):
   ```hledger
   ~ monthly from YYYY/MM/01 to YYYY/MM+1/01  ; Ramadan YEAR (starts Mon DD)
       Expenses:family:ramadan-food            USD     150.00
       Expenses:charity:ramadan-sadka          USD     270.00
       Assets:bank:<account>
   ```

   If Ramadan starts very early in the month (before ~5th), combine pre-Ramadan into this rule:
   ```hledger
   ~ monthly from YYYY/MM/01 to YYYY/MM+1/01  ; Ramadan YEAR (starts Mon DD) + pre-buildup
       Expenses:family:ramadan-food            USD     150.00
       Expenses:charity:ramadan-sadka          USD     420.00  ; combined pre + during only (NOT tail — tail $60 stays in the Eid al-Fitr rule)
       Assets:bank:<account>
   ```

   **Eid al-Fitr month** (month containing Eid al-Fitr):
   ```hledger
   ~ monthly from YYYY/MM/01 to YYYY/MM+1/01  ; Eid al-Fitr YEAR (Mon DD) + tail of Ramadan
       Expenses:charity:ramadan-sadka          USD      60.00
       Expenses:zakat:fitr                     USD      75.00  ; <currency> X per person × N family members
       Expenses:gifts:eid                      USD      90.00
       Assets:bank:<account>
   ```

   **Eid al-Adha month** (month containing Eid al-Adha):
   ```hledger
   ~ monthly from YYYY/MM/01 to YYYY/MM+1/01  ; Eid al-Adha YEAR (Mon DD)
       Expenses:gifts:eid                      USD      90.00
       Expenses:family:od7ia                   USD    400.00  ; udhiyah / sacrifice — adjust to local livestock prices
       Assets:bank:<account>
   ```

3. **Important rules**:
   - Each rule spans exactly ONE month.
   - If Ramadan and Eid al-Fitr fall in the same Gregorian month, combine them into one rule.
   - Comment each rule with the actual Gregorian date of the event.
   - Use 4-space indent for postings.
   - Total Ramadan sadaqah across all rules in the example defaults sums to ~USD 480 (150 pre + 270 during + 60 tail). Scale to the user's chosen amount.

4. **Output format**: Show the rules as a hledger journal block ready to paste into the user's `periodic.journal` (or equivalent) under the appropriate year section. Include comments noting the Gregorian dates of each Hijri event so the rules are self-documenting. Remind the user that:
   - Amounts are suggested defaults — adjust for family size, locale, and budget.
   - Account names are conventions — rename to match their existing taxonomy if different.
   - The udhiyah amount in particular is highly locale-dependent (livestock prices vary widely).

5. **Ask before editing**: Show the generated rules and get explicit confirmation before modifying `periodic.journal` or any other journal file.

## Notes and common pitfalls

- **Hijri date precision**: Published Hijri-Gregorian conversions are approximate. The actual start of Ramadan and the Eid days depend on moon sighting and may differ by ±1 day from the calculated date. The rule comments should reflect the most widely accepted date for the user's locale.
- **Same-month collisions**: Around 2030 and again in the late 2030s, Ramadan and Eid al-Fitr will both fall within a single Gregorian month for some calendars. Combine those rules.
- **Zakat al-fitr**: This is a per-person obligation paid before the Eid al-Fitr prayer, typically the cash equivalent of ~2.5 kg of staple food per household member. The placeholder `<currency> X per person × N family members` is meant to be replaced with the user's local rate.
- **Account naming**: If the user already has `Expenses:religious:*` or `Expenses:islamic:*` trees, prefer those over inventing a new `charity:` subtree. Run `hledger accounts` first.
- **Cross-skill reference**: If a `/new-year-budget` skill is available in the user's setup, this skill is typically invoked from there as part of yearly setup.
- **Scope**: This skill covers Hijri-event-linked obligations (Ramadan sadaqah, Eid gifts, udhiyah, zakat al-fitr). It does NOT calculate zakat al-mal (the annual 2.5% wealth zakat), which is hawl-anniversary based — handle that separately.
