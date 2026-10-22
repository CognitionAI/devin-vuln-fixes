# Vulnerability fix skills

Three [Agent Skills](https://agentskills.io) that find and patch vulnerable runtimes across your repos. They work in any coding agent that reads `SKILL.md` files: Devin, Claude Code, Codex, Cursor, Factory Droid, GitHub Copilot, Gemini CLI, OpenCode, Windsurf and [70 more](https://github.com/vercel-labs/skills/blob/main/docs/agents.md).

| Skill | What it fixes | Fixed releases |
|---|---|---|
| [`fix-php-vuln`](skills/fix-php-vuln/SKILL.md) | PHP2Shell: CVE-2026-109618 and CVE-2026-109619, unauthenticated RCE in every web SAPI | 8.2.35, 8.3.36, 8.4.27, 8.5.12 |
| [`fix-node-vuln`](skills/fix-node-vuln/SKILL.md) | Node.js October 21, 2026 security release, incl. CVE-2026-111497 (Brotli RCE); flags end-of-life Node 20 | 22.23.4, 24.22.1, 26.12.1 |
| [`fix-gitlab-vuln`](skills/fix-gitlab-vuln/SKILL.md) | CVE-2026-110264, CVSS 10.0 unauthenticated RCE in self-managed GitLab (Omnibus, Docker, Helm, IaC) | 19.5.1, 19.4.3, 19.3.6, 19.2.9, 19.1.9, 19.0.10, 18.11.13 |

Each skill follows the same shape:

1. **Inventory, read-only.** Search Dockerfiles, version files, CI config, Helm charts, Terraform, Ansible, Chef and Puppet. Produce one table: file, current version, pin style, production or CI-only, test coverage, confidence.
2. **Stop at an approval gate.** Nothing changes until you pick the repos to patch.
3. **Patch to a confirmed target.** The agent checks the fixed tag, digest or package exists before it edits. It never guesses versions.
4. **Test before and after.** Baseline build and tests, then the same tests inside the new production image. New failures and new deprecation warnings count as regressions.
5. **One focused PR per repo** with before and after, digests, test results, risk label, rollout plan and rollback target.

## Install

### Any agent, with `npx skills`

```bash
npx skills add CognitionAI/devin-vuln-fixes
```

The CLI detects the agents on your machine and asks where to install. Add `-g` to install for every project, or pick agents directly:

```bash
npx skills add CognitionAI/devin-vuln-fixes -a claude-code -a codex -a cursor -a droid -a devin
```

Install one skill only:

```bash
npx skills add CognitionAI/devin-vuln-fixes --skill fix-php-vuln
```

Update later with `npx skills update`. Requires Node.js 22.20 or later.

### Claude Code (plugin marketplace)

```
/plugin marketplace add CognitionAI/devin-vuln-fixes
/plugin install vuln-fixes@cognition-skills
```

### Codex (plugin marketplace)

The repo carries a Codex plugin manifest (`.codex-plugin/plugin.json`) and marketplace (`.agents/plugins/marketplace.json`). Add it as a marketplace in Codex, then install **Vulnerability Fixes** from `/plugins`. Codex also reads `.agents/skills/`, so `npx skills add CognitionAI/devin-vuln-fixes -a codex` works without the plugin.

### Factory Droid (plugin marketplace)

```bash
droid plugin marketplace add https://github.com/CognitionAI/devin-vuln-fixes
droid plugin install vuln-fixes@cognition-skills
```

Droid also reads skills that `npx skills add -a droid` installs into `.agents/skills/`.

### Devin

- **Devin cloud:** install **Vulnerability Fixes** from the plugin marketplace, or add this repo on the Customize page (**From repository**, `CognitionAI/devin-vuln-fixes`). Devin also discovers `.agents/skills/` in every connected repo, so a project install with `npx skills` works too.
- **Devin CLI and Devin Desktop:** `npx skills add CognitionAI/devin-vuln-fixes -a devin`.

### By hand

Copy a skill folder into your agent's skills directory. The common locations are `.agents/skills/`, `.claude/skills/`, `.devin/skills/`, `.factory/skills/` and `.windsurf/skills/`:

```bash
git clone https://github.com/CognitionAI/devin-vuln-fixes
cp -r devin-vuln-fixes/skills/fix-php-vuln .agents/skills/
```

## Use

Ask in plain words. The agent picks the skill from its description:

- "Please fix my PHP vulnerability."
- "Are any of our repos on a vulnerable Node version? Patch the ones that are."
- "Upgrade our self-managed GitLab for CVE-2026-110264."

Or invoke directly where your agent supports slash commands: `/fix-php-vuln`, `/fix-node-vuln`, `/fix-gitlab-vuln`.

The agent needs read access to the repos, and for the build and test steps it needs Docker and access to the image registry or package mirror your CI already uses. When public hosts are blocked, the skills tell the agent to mark a target as unverified and let CI prove it.

## Why the skills look the way they do

- **Approval gate.** Patching a runtime is a production change. The skills never edit before you approve a list of repos.
- **No guessed versions.** Every tag, digest and package release is confirmed to exist. "Fixed" means the artifact contains the fix, not that a version string looks newer.
- **Tests inside the image.** Host-side tests miss base-image changes. The skills run the suite inside the built production image, before and after.
- **Agent-neutral wording.** The skills do not depend on one agent's tools. Where an agent supports subagents, the skills use one per repo; otherwise they work repo by repo.
- **No `allowed-tools`.** Agents disagree on the format of that field (space-separated string in the spec, YAML list in Devin CLI). Leaving it out keeps the skills portable. Add it in a fork if you want tools pre-approved.

## Keeping the targets current

The fixed-version numbers are pinned to the advisories of October 21, 2026. When a vendor ships a newer patch, we update the skill and bump `metadata.version` in its frontmatter. See [CHANGELOG.md](CHANGELOG.md). Run `npx skills update` to pick up changes; `skills-lock.json` records the hash you have.

## Repository layout

```
skills/<name>/SKILL.md        the skills (Agent Skills spec; this is what every agent reads)
.claude-plugin/               Claude Code plugin + marketplace manifests (also read by Factory and npx skills)
.codex-plugin/ + .agents/plugins/   Codex plugin + marketplace manifests
.factory-plugin/              Factory Droid native manifests
.devin-plugin/plugin.json     Devin plugin manifest
```

## Contributing and security

See [CONTRIBUTING.md](CONTRIBUTING.md) to propose a change or a new skill.
