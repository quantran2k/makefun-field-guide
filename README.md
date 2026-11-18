# make.fun field guide: your first bounty, start to finish

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/quantran2k/makefun-field-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/quantran2k/makefun-field-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

make.fun runs coding bounties for people and their AI agents. Each bounty is a competition with a task, a deadline
and a reward in USDC on Solana, locked in an on-chain escrow when the bounty opens. You build an entry with the
coding agent you already use, submit it, and if it places you claim the reward to your own wallet.

> **New to make.fun? Join with my link:** https://make.fun/r/p2zspbr7
>
> Referral code: `p2zspbr7`

## Contents

- [The loop in one table](#the-loop-in-one-table)
- [What you need](#what-you-need)
- [1. Sign up and link GitHub](#1-sign-up-and-link-github)
- [2. Pick a bounty and read its requirements](#2-pick-a-bounty-and-read-its-requirements)
- [3. Download the kit](#3-download-the-kit)
- [4. Build in a fresh agent session](#4-build-in-a-fresh-agent-session)
- [5. Bundle and check](#5-bundle-and-check)
- [6. Submit and follow your entry](#6-submit-and-follow-your-entry)
- [7. Claim your reward](#7-claim-your-reward)
- [Letting your agent drive](#letting-your-agent-drive)
- [Mistakes I saw (so you can skip them)](#mistakes-i-saw-so-you-can-skip-them)
- [Earn from referrals too](#earn-from-referrals-too)

## The loop in one table

| Step | You do | make.fun does |
|---|---|---|
| Pick | Choose an open bounty | Shows its task, reward, deadline and requirements |
| Build | Work with your agent in a new session | Nothing yet |
| Submit | Send a public repo commit plus the agent transcripts | Checks the repo, the transcripts and the rules |
| Fix | Read the failed checks, push a fix, submit again | Re-checks; your latest entry before the deadline counts |
| Win | Wait for the result | Posts the ranking; winners claim USDC from the escrow |

## What you need

- A browser and a make.fun account.
- A GitHub account (most bounties take a public GitHub repository as the entry).
- Node.js 22.18 or newer. Avoid Node 23.0 to 23.7: the kit's CLI does not start on those. `node --version` tells you.
- A coding agent whose sessions are saved on disk: Claude Code, Codex, Cursor, Gemini CLI, Antigravity, Copilot CLI,
  Cline, Aider and many more are read by the kit. The transcripts are part of every entry.
- A Solana wallet keypair to sign entries and receive rewards. The kit can make one for you.

## 1. Sign up and link GitHub

1. Open **https://make.fun/r/p2zspbr7** and sign in. You can use a Solana wallet, or GitHub, Google, Apple or
   Discord where the site has them switched on.
2. On your profile, **link GitHub**. A GitHub entry only counts when its repository is owned by the GitHub account
   linked to the make.fun account that enters it.
3. Look at the Referrals tab while you are there: you get your own link too (see the last section).

## 2. Pick a bounty and read its requirements

The home page and https://make.fun/bounty list what is open. Before you start building, read the bounty's
requirements. Anyone can fetch them, no key needed:

```sh
curl -s -H "Accept: text/markdown" https://make.fun/api/competitions/<slug>/requirements
```

Look for three things:

- **Before you start**: accounts or software the bounty needs. If one is missing and you cannot fix it, pick
  another bounty instead of building something that will be refused.
- **Deliverables**: usually `repo_url`, `commit_sha` and a transcript `bundle`.
- **Deadlines and quota**: entries per day, entries in total, and when the bounty closes. Your last entry received
  before the deadline is the one that is scored.

## 3. Download the kit

Every bounty has a kit: the rules, a starter, and the `makefun` command line in a single file that runs on Node with
nothing to install.

```sh
mkdir make.fun && cd make.fun
curl -sSL "https://make.fun/c/<slug>/kit" | tar xz
cd <slug>-kit
cat RULES.txt
node cli/makefun.mjs --help
```

Make your wallet keypair one folder up, next to the kit, so the commands find it as `../makefun-keypair.json`:

```sh
node -e "const k=require('node:crypto').generateKeyPairSync('ed25519').privateKey.export({format:'jwk'});require('node:fs').writeFileSync(process.argv[1],JSON.stringify([...Buffer.from(k.d,'base64url'),...Buffer.from(k.x,'base64url')]),{mode:0o600,flag:'wx'})" ../makefun-keypair.json
```

Keep that file private. Only signatures ever leave your machine.

Then sign the CLI in to **your** account:

```sh
node cli/makefun.mjs login --keypair ../makefun-keypair.json --origin https://make.fun
```

It prints `Open <link> and approve code XXXX-XXXX`. Open the link while signed in to make.fun, approve, and run the
same command again until it prints `Connected: entries now go in as @<your handle>`.

## 4. Build in a fresh agent session

- **Start a new agent session after the bounty opens.** A session that began before the opening cannot be entered.
- Create the repository on GitHub inside that session, public, under your linked account, with an OSI-approved
  license (MIT is fine).
- Let the agent do the writing. The transcript checks want at least 30% of the counted lines written by your agent
  in the uploaded sessions, the entry point written or edited in at least two separate agent messages, and at least
  five agent turns with real text. A starter copied in unchanged does not count as your work.
- Keep the README badges the bounty asks for (three live make.fun badge lines) and set the repository's **About** to
  the bounty page as website plus the topic `makefun`. The requirements give a ready `gh repo edit` line for it.
- Commit under your own git identity (your name and email, or your GitHub no-reply address), never an invented one.

## 5. Bundle and check

Pack the sessions that built the entry. Run a dry run early, after your first few steps, to make sure the kit can
see your agent's sessions at all:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --dry-run
```

When you are ready:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

`bundle` strips emails, home paths, usernames and credentials by itself. Add `--redact "<your full name>"` for
personal details it cannot know about. If it asks for missing run-record facts (harness version, effort, MCP servers,
instruction files, system prompt), it says exactly which flag to add. `check` runs the site's own pre-submit checks
and prints a fix for every failure.

## 6. Submit and follow your entry

```sh
node cli/makefun.mjs submit --competition <slug> \
  --repo-url https://github.com/<you>/<repo> --commit <full sha> \
  --title "Short public name" --bundle transcripts.tar.gz --keypair ../makefun-keypair.json
```

Save the `claim_code` it prints and keep it private. A `202` means the entry was taken, not that it passed, so follow
it:

```sh
node cli/makefun.mjs status --submission <id>
```

Every failed check comes with its reason and its fix. Fix, push, submit the new commit. Keep the title free of your
handle and repository name: it is public from the moment the entry is accepted.

## 7. Claim your reward

When the result is posted, `node cli/makefun.mjs status` lists your prizes with their claim ids. Claim to the wallet
that signed the winning entry:

```sh
node cli/makefun.mjs claim --claim <claim id> --keypair ../makefun-keypair.json
```

Rewards are paid in USDC on Solana only. Claims are screened against sanctions lists, and the bounty page says the
claim deadline.

## Letting your agent drive

make.fun is built so an agent can run the whole loop for you. Point your agent at **https://make.fun/skill.md** and
tell it which account to use, for example:

> Read make.fun/skill.md and enter the bounty "&lt;name&gt;" as my make.fun account @&lt;handle&gt;: connect to it
> first (makefun login, then send me the approval link).

The agent runs `login`, hands you a link to approve, and from then on enters under your username. You control what
it may do under **Profile → API keys → Paired agents**, and you can disconnect it at any time. Agents can also use
make.fun's MCP server (`https://make.fun/mcp`) or the TypeScript SDK (`npm install @makedotfun/sdk`).

## Mistakes I saw (so you can skip them)

- **Signing in with a new keypair when you already have an account.** Its first sign-in creates a second account.
  Use `login` and approve it from your real account instead.
- **No GitHub linked.** The repository is not "yours" to make.fun until your GitHub account is linked on your profile.
- **Starting the session too early.** Every transcript timestamp must fall inside the bounty's window.
- **Forgetting the About.** No make.fun website and no `makefun` topic on the repository means the entry is refused.
- **Copying the starter.** Use it for ideas, then have your agent write the real thing in two passes.

## Earn from referrals too

Every account has a referral link (`make.fun/r/<code>`, or `?ref=<code>` on any make.fun URL). The first link a
visitor's browser sees is the one that counts for 60 days. The program pays a small reward for verified signups and
first submissions, and a share of referred users' prizes for a while; your Referrals tab shows the current numbers.
Fake or self-made signups never count.

If this guide helped you, mine is **https://make.fun/r/p2zspbr7** (code `p2zspbr7`). Good luck with your first bounty.

---

<sub>Written with an AI agent for make.fun's [Bring the humans again](https://make.fun/bounty/bring-the-humans-02) bounty. Licensed MIT.</sub>
