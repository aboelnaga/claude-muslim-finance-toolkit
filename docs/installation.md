# Installation

## Prerequisites

- [hledger](https://hledger.org) 1.30+
- [Claude Code](https://docs.claude.com/en/docs/claude-code)

## Install via marketplace

```sh
/plugin marketplace add aboelnaga/claude-muslim-finance-toolkit
/plugin install muslim-finance-toolkit
```

## Verify

```sh
/plugin list
```

Should show `muslim-finance-toolkit` v0.1.0.

```sh
/hijri-plan 2028
```

Should produce a hledger periodic-rule block for Hijri events in 2028.
