# Contributing

Thanks for helping build this collection! This repo has one focus: **multi-event correlation rules**. Keeping that focus tight is what makes it useful, so please read the scope below before opening a PR.

## What belongs here

✅ **Yes**
- Correlation rules that need **more than one event** to fire (`event_count`, `value_count`, `temporal`, `temporal_ordered`, and aggregation types).
- Correlation **chains** (one correlation feeding another).
- The base single-event rules a correlation depends on — included **in the same file** as the correlation, referenced by `name`.

❌ **No**
- Standalone single-event detection rules. If a rule fires on one event with no correlation block, it doesn't belong here on its own.

## Rule requirements

Every contributed rule must:

1. Be valid **Sigma v2 correlation format**.
2. Be **self-contained** — base rule(s) + correlation block in one `.yml` file, base rules referenced by `name`.
3. Have a fresh **UUID v4** in the final correlation rule's `id` field (generate one, don't reuse).
4. Include this metadata on the correlation rule:
   - `title` — clear and specific
   - `id` — UUID v4
   - `status` — `experimental` for new rules (`test` / `stable` once validated)
   - `description` — what it detects and why it matters
   - `author` — your name/handle (and original author if ported)
   - `references` — links (MITRE technique pages, blog posts, source rule)
   - `tags` — relevant `attack.*` MITRE ATT&CK tags
   - `falsepositives` — realistic FP scenarios
   - `level` — `low` / `medium` / `high` / `critical`
5. Be tagged with at least one **MITRE ATT&CK** technique.

## File naming

Use lowercase, words separated by underscores: `platform_short-description.yml`

Examples: `win_bruteforce_success.yml`, `linux_ssh_spray_then_success.yml`, `aws_console_login_after_failed_mfa.yml`

Place files under the matching platform folder: `rules/windows/`, `rules/linux/`, `rules/cloud/`, etc.

## Porting rules from other projects

You're encouraged to port correlation logic from SigmaHQ, Splunk Security Content, Elastic detection-rules, and similar. When you do:

- **Credit the original author** in `author` and link the source in `references`.
- **Check the source license** is compatible with this repo's license (DRL). Don't copy rules whose license forbids redistribution.
- Re-express the logic in Sigma correlation format — don't just paste SPL/EQL into a description.

## Validating before you submit

Run a quick local check with [`sigma-cli`](https://github.com/SigmaHQ/sigma-cli):

```bash
pip install sigma-cli
sigma check rules/windows/your_rule.yml          # validates structure
sigma convert -t splunk rules/windows/your_rule.yml   # sanity-check it compiles
```

If you can, note in your PR what data/environment you tested against and what tuning the thresholds may need.

## Pull request process

1. Fork the repo and create a branch.
2. Add **one rule per PR** where possible — it keeps review easy.
3. Use a descriptive PR title, e.g. `Add: Kerberoasting followed by lateral movement (Windows)`.
4. In the PR description, briefly explain what the rule detects and any known false positives or tuning notes.
5. A maintainer will review for scope, correctness, and metadata completeness.

## Be respectful

Be kind and constructive in issues and reviews. We're all here to help defenders catch more, faster.
