# Usage

## /hijri-plan

Generate hledger periodic rules for Hijri-linked events in a Gregorian year.

### Inputs
- Gregorian year (required argument, e.g. `/hijri-plan 2028`)
- Currency (read from `CLAUDE.md` if present, else asked)
- Daily Ramadan sadaqah amount (asked, with sensible default)
- Eid gift / udhiyah amounts (asked, with sensible defaults)

### Output
- A block of valid hledger periodic transaction rules for the year, ready to paste into your `periodic.journal`

### Try it on the sample data

```sh
cd examples/
/hijri-plan 2028
```

The skill produces rules for Ramadan daily sadaqah, Eid al-Fitr gifts/zakat-fitr, and Eid al-Adha udhiyah/gifts.
