# claude-muslim-finance-toolkit

Claude Code skills for Muslim [hledger](https://hledger.org) users.

## What it does

- **`/hijri-plan`** — Generate hledger periodic budget rules for Hijri-linked events (Ramadan daily sadaqah, Eid al-Fitr/Adha gifts, udhiyah / Eid sacrifice) for any Gregorian year. Output is a valid hledger journal block ready to paste into your `periodic.journal`.

## Install

```sh
/plugin marketplace add aboelnaga/claude-muslim-finance-toolkit
/plugin install muslim-finance-toolkit
```

## Alternative: copy skills (no plugin install)

If you can't install plugins (e.g., on Claude Code mobile app, restricted environments) or prefer skills checked into your repo:

```sh
git clone https://github.com/aboelnaga/claude-muslim-finance-toolkit
cp -r claude-muslim-finance-toolkit/skills/hijri-plan your-project/.claude/skills/
```

Skill will then be available as `/hijri-plan` directly (no plugin namespace prefix). Update by re-cloning and re-copying when you want a new version.

## 5-minute walkthrough

```sh
git clone https://github.com/aboelnaga/claude-muslim-finance-toolkit
cd claude-muslim-finance-toolkit/examples

# Open Claude Code in this directory and run:
/hijri-plan 2028
```

The skill produces a hledger periodic-rule block for 2028's Hijri events.

## Roadmap

See [TODO.md](TODO.md). Backlog skills:
- `/zakat-calc` — calculate zakat al-mal against current net worth on hawl date
- `/financial-advisor` — Islamic-compliant financial advisor (in design)

## License

MIT — extracted from a personal hledger workflow.
