# Correlation Rules

A community-driven collection of **multi-event correlation detection rules** written in [Sigma](https://github.com/SigmaHQ/sigma) format — built for threat hunters, detection engineers, and defenders.

> **Scope in one line:** if a detection needs *more than one event* to fire, it belongs here. Single-event rules do not.

---

## Why this repo exists

Single-event detection rules are everywhere — SigmaHQ, Elastic, Splunk ESCU and others all publish thousands of them. But the **correlation logic** (brute force *followed by* a success, password spraying across many accounts, a burst of recon commands, lateral movement chains) is scattered, mixed in among single-event rules, and expressed in incompatible backend-specific syntax.

This repo does one thing: it collects **correlation / multi-event rules only**, in a single consistent format, so they're easy to find, read, test, and reuse.

## Scope

**In scope**
- Multi-event correlation rules: `event_count`, `value_count`, `temporal`, `temporal_ordered` (and newer aggregation types).
- Correlation *chains* (one correlation feeding another).
- The base single-event rules that a correlation references — but only as building blocks, kept in the same file as the correlation that uses them.

**Out of scope**
- Standalone single-event rules. (Plenty of great repos already cover those.)

## Format

All rules are authored in the **Sigma v2 correlation format**, which is the source of truth. Each file is self-contained: the base rule(s) plus the correlation block live together, with base rules referenced by their `name`.

Correlation types in use:

| Type | What it catches |
|------|-----------------|
| `event_count` | The same rule firing N+ times in a window (e.g. 10+ failed logons per account). |
| `value_count` | N+ distinct values of a field (e.g. failures against 10+ different accounts from one source). |
| `temporal` | A set of different rules firing close together in any order. |
| `temporal_ordered` | A set of different rules firing close together in a specific order. |

## Repository layout

```
correlation-rules/
├── rules/
│   ├── windows/      # Windows-based correlations
│   ├── linux/        # (coming)
│   └── cloud/        # (coming)
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

## What's here now

| Rule | Type | Detects |
|------|------|---------|
| `win_bruteforce_success` | chain (`event_count` → `temporal_ordered`) | Many failed logons followed by a success for the same account. |
| `win_password_spraying` | `value_count` | One source failing against many distinct accounts. |
| `win_recon_command_burst` | `temporal` | Multiple discovery commands by the same user in a short window. |

More to come — contributions welcome.

## Using the rules

These are Sigma rules, so you can convert them to your SIEM's query language with [`sigma-cli`](https://github.com/SigmaHQ/sigma-cli) / pySigma:

```bash
pip install sigma-cli
sigma plugin install splunk        # or: elasticsearch, etc.

# Convert a correlation rule to Splunk SPL
sigma convert -t splunk rules/windows/win_bruteforce_success.yml
```

> ⚠️ **Note:** not every backend supports every correlation type, and some lose event-ordering or use fixed time windows. Always review and test the generated query before deploying. Treat thresholds and timespans as starting points — tune them to your environment.

## Contributing

Contributions are very welcome — porting correlation rules from other projects (with attribution) or writing your own. See **[CONTRIBUTING.md](CONTRIBUTING.md)** for the rule template, required metadata, and PR process.

## License

Rules are released under the **[Detection Rule License (DRL)](https://github.com/SigmaHQ/Detection-Rule-License)** — see [LICENSE](LICENSE). When porting rules from other sources, keep the original author and source in the rule metadata and make sure the source license is compatible.

## Credits

Built on the work of the [SigmaHQ](https://github.com/SigmaHQ/sigma) project and the wider detection-engineering community. Where rules are adapted from Splunk Security Content, Elastic detection-rules, or other sources, the original authors are credited in each rule's `references`/`author` fields.
