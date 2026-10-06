# renovate-config

The shared [Renovate](https://docs.renovatebot.com) preset for Team Riley sites
(both the `Team-Riley-Web` org and `TheRileyBird` account). One file,
`default.json`, decides how every site is kept up to date; change it here and
every repo follows on its next run.

## What it does

- **Patch and minor updates auto-merge** once all status checks pass. On our
  sites the check is Netlify's deploy preview, so a PR only merges if the site
  still builds. A repo with no checks keeps its PRs open for a person.
- **Majors never auto-merge.** Tailwind 3 to 4 or an Astro major changes
  config; they arrive as a PR labelled `major`.
- **Grouped PRs**: Astro + `@astrojs/*`, Tailwind, Alpine, and test tooling
  each move together, so a site gets a few PRs, not twenty.
- **3-day quarantine** on new npm releases (`minimumReleaseAge`) so a
  just-published bad release or supply-chain incident is caught upstream
  first. Security fixes bypass the quarantine.
- **Rebases only on conflict** (`rebaseWhen: conflicted`). The default for
  auto-merging PRs is to rebase every open one each time the base branch
  moves, which re-runs every check; on a busy repo that used up the org's
  GitHub Actions allowance (2026-10-06).
- **Runs overnight** (Central time), monthly lockfile maintenance, and a
  Dependency Dashboard issue per repo listing everything pending.
- `@team-riley/shopify` (pinned git tag in Rosario and CFC) gets a PR per new
  tag, never auto-merged: bump, then run that site's `test:unit` + `test:e2e`.

## Setup

1. Install the Renovate GitHub App (https://github.com/apps/renovate) on the
   org / account, "All repositories". Only an owner can do this.
2. Renovate opens an **onboarding PR** in each repo. For `Team-Riley-Web`
   repos it proposes this preset automatically (org default). For
   `TheRileyBird` repos, make the onboarding PR's `renovate.json` read:

   ```json
   { "$schema": "https://docs.renovatebot.com/renovate-schema.json", "extends": ["github>Team-Riley-Web/renovate-config"] }
   ```
3. Merge the onboarding PR. Nothing changes in a repo until it is merged.

Validate edits with `npx --yes --package renovate renovate-config-validator default.json`.
