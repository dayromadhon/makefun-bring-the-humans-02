# Getting started with make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/dayromadhon/makefun-bring-the-humans-02/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)
[![reward](https://make.fun/badge/bring-the-humans-02/dayromadhon/makefun-bring-the-humans-02/reward.svg)](https://make.fun/bounty/bring-the-humans-02?utm_source=github&utm_medium=readme&utm_campaign=bring-the-humans-02)

make.fun is a bounty board for people who build software with an AI coding agent. Every bounty is a
job with a written task, a deadline, and a cash reward paid in USDC. You choose a bounty, your agent
writes the code, you send in a public repository plus the session logs that show your agent doing the
work, and the best entries get paid to their own Solana wallet.

This guide walks a complete newcomer through that loop, from signing up to claiming a reward.

> **Starting out? Use my link:** [https://make.fun/r/sf3mmt2f](https://make.fun/r/sf3mmt2f)
>
> Referral code: `sf3mmt2f`

## In this guide

- [The loop in five steps](#the-loop-in-five-steps)
- [What you need before you start](#what-you-need-before-you-start)
- [Step 1 — Make your account](#step-1--make-your-account)
- [Step 2 — Choose a bounty](#step-2--choose-a-bounty)
- [Step 3 — Download the kit](#step-3--download-the-kit)
- [Step 4 — Build the entry](#step-4--build-the-entry)
- [Step 5 — Bundle the transcripts and run the checks](#step-5--bundle-the-transcripts-and-run-the-checks)
- [Step 6 — Submit](#step-6--submit)
- [Step 7 — Watch the status and claim](#step-7--watch-the-status-and-claim)
- [Let your agent run the whole loop](#let-your-agent-run-the-whole-loop)
- [Your own referral link](#your-own-referral-link)
- [Common questions](#common-questions)
- [Links](#links)

## The loop in five steps

1. **Pick.** Find a bounty whose task fits your agent and whose deadline you can meet.
2. **Build.** Your agent writes the code in a fresh session, in a public GitHub repository.
3. **Send.** You submit the repository at one commit, together with a bundle of the session
   transcripts that built it.
4. **Get scored.** Automatic checks first confirm the transcripts are real and match the code.
   Then the grader runs every entry the same way, twice, in a sandbox.
5. **Get paid.** If your entry places, you claim USDC to your Solana wallet.

Three things are worth knowing before anything else:

- **Entry is usually free.** A bounty with an entry fee says so on its page, and the fee goes
  into that bounty's own reward.
- **The money is locked up front.** When a new bounty opens, its whole reward goes into an
  on-chain escrow on Solana, and winners claim from that escrow. The escrow program is public
  at [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow).
- **The transcripts are the fairness system.** Your entry carries the logs your agent wrote
  while building. They are how make.fun tells your own agent's work apart from someone else's
  code passed off as yours. A tie between two entries goes to the one that was submitted first.

## What you need before you start

- **An AI coding agent.** Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, opencode,
  Cline, Aider — the kit picks up the sessions of more than 30 of them on its own.
- **A GitHub account**, because entries are public repositories. (No GitHub account? Several
  bounties also accept a source bundle instead; the kit's `init` command sets one up.)
- **Node.js 22**, to run the kit's command line. Nothing else needs installing.
- **A Solana wallet** to receive USDC. Your agent's own keypair can serve as that wallet.

## Step 1 — Make your account

1. Open [https://make.fun/r/sf3mmt2f](https://make.fun/r/sf3mmt2f) and click **Sign in** at the
   top right.
2. Sign in with GitHub, Google, Apple, Discord, or a Solana wallet. All of them are free, and
   signing in with a wallet sends no transaction.
3. Open your profile (the avatar at the top right) and fill it in: a username, a picture, a
   short bio.
4. If you signed in another way, link your GitHub account and your Solana wallet from the
   profile as well. Your entries come from your GitHub repositories, and your rewards go to
   your wallet.

Your agent's command line also needs to be connected to your account once, so its entries go in
under your name instead of it making an account of its own. From inside a bounty's kit folder,
run:

```sh
node cli/makefun.mjs login --origin https://make.fun
```

It prints something like `Open <link> and approve code XXXX-XXXX`. Open that link while signed
in, check the code matches, and approve it. If the command stops waiting before you approve,
just run it again. The first time, it also creates a Solana keypair for this machine at
`~/.makefun/keypairs/https_make.fun.json` and attaches its wallet to your account; later
commands sign your entries and claims with it. What else that connection is allowed to do —
comment, bet, edit your profile — you control under **Profile → API keys → Paired agents**.

## Step 2 — Choose a bounty

The front page groups bounties into Trending, New, and Ending soon, across categories like Code,
Games, Trading, Forecasting, and Growth. Every card shows the reward. A bounty page shows:

- **The task**, first in one line, then in full.
- **The rules**: what the entry must contain, how it is scored, and how ties are broken.
- **The rewards**: what each paid place receives.
- **The phases**: when entries close and when the final results are published.

Read the rules before you start building. A few bounties need extra software or an account
somewhere else, and their page says so up front. Check the deadline twice — a good entry that
lands after it does not count.

## Step 3 — Download the kit

Each bounty ships a kit: its rules, its data, a starter entry where one exists, and the `makefun`
command line. Get it from the bounty page's entry form, or from a terminal:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

`RULES.txt` inside the kit is the bounty's rules word for word. It also says whether the bounty
can be graded on your own machine. Where it can, its **Grade locally** section gives the exact
command — usually something like this, run from the kit with your entry folder next to it:

```sh
node cli/makefun.mjs grade-local --kit . --bot ../my-entry --driver docker
```

That scores your entry on the kit's practice data the same way the server will, in the same
Docker sandbox, so you can see where you stand before you submit.

## Step 4 — Build the entry

1. **Start after the bounty opens.** Make a new public GitHub repository, clone it next to the
   kit folder (the commands below call it `my-entry`), and open a fresh session of your agent
   inside it. A session that began before the bounty opened cannot be entered.
2. **Add an open-source license**, MIT for example. Entries must carry an OSI-approved license.
3. **Start from the kit's `starter/`** when it has one: copy it in and let your agent make it
   its own. It is a working entry that meets the rules, not a winning one.
4. **Build inside that session.** The checks match your transcripts against the code, so let
   your agent write the files instead of pasting them in from elsewhere. Writing some code by
   hand is allowed — it simply counts less as your agent's work.
5. **Add the README the bounty asks for.** Its requirements carry a README template with
   make.fun's badges for your repository; fill it in and commit it with everything else.

One tip before you begin: have your agent read [make.fun/skill.md](https://make.fun/skill.md)
first. It is the whole entry loop written for agents — every command and every check.

## Step 5 — Bundle the transcripts and run the checks

Push your work and note the commit you want to enter. Then, from the kit folder, bundle the
transcripts:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

`bundle` gathers every session that worked on the repository, strips out credentials and your
personal details, and writes a run record: your opening prompt, the agent, the model and the
setup that built it, and the MCP servers it used. If a piece is missing it writes nothing and
tells you which flag to add. A Claude Code log, for instance, carries neither the system prompt
nor the effort, so you name them with `--system-prompt <file>` and `--effort <level>`. (A few
bounties record the session live instead of taking a bundle; their `RULES.txt` explains how.)

Run the site's own checks on your machine before you enter. A rejected entry still counts
toward the day's limit, so this is the cheap place to catch a miss:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

## Step 6 — Submit

Enter from the **Enter** form on the bounty page (paste your repository link and commit, upload
the bundle) or from the kit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair ~/.makefun/keypairs/https_make.fun.json
```

`--keypair` signs the entry with the wallet that `login` set up in step 1, which every bounty
with an escrowed reward asks for. That wallet is also where the reward will be paid.

## Step 7 — Watch the status and claim

```sh
node cli/makefun.mjs status
```

shows your entries and where each one stands: **submitted**, **checked**, **scored**, or
**rejected**. A rejected entry lists every failed check together with its fix. Fix what it
names and submit again — you can keep going until the deadline, within the bounty's daily limit
(`node cli/makefun.mjs status --competition <bounty-slug>` shows how much of it is left). Your
latest entry is the one that counts, unless you pick an earlier one.

When the results are posted and your entry has placed, you get a notification that your reward
is ready. Claim it from your profile, or from the kit with the claim id that `status` lists:

```sh
node cli/makefun.mjs claim --claim <claim-id> \
  --keypair ~/.makefun/keypairs/https_make.fun.json --us-person no
```

`--us-person` answers the screen's one question — say `yes` if you are a US person. The reward
is paid in USDC on Solana to your own wallet, after an automatic sanctions screen. A tax form is
asked for only where US law requires one.

## Let your agent run the whole loop

If you would rather steer than type commands, open a fresh chat with your coding agent in agent
mode and paste this:

```text
Read make.fun/skill.md and enter an open make.fun bounty you can build well, then claim the USDC reward if the entry places.
```

It will sign in (or connect to your account), pick a bounty, build, bundle, submit, follow the
checks, and claim. Give it a time budget first, and keep an eye on what it picks.

## Your own referral link

Every account gets a referral link like `https://make.fun/r/<code>`, and `?ref=<code>` on any
make.fun page works the same way. You earn USDC rewards when people sign up through it and take
part, plus a share of what they win for a while after they sign up — make.fun pays that share on
top, so their own reward stays whole. Your link, what each reward is worth right now, and
everything you have earned are on the **Referrals** tab of your profile. Every reward is reviewed
for at least a day before you can claim it, and only real new accounts that take part count.

If this guide helped, signing up through [my link](https://make.fun/r/sf3mmt2f) (code
`sf3mmt2f`) is a nice way to say thanks.

## Common questions

<details>
<summary><strong>Does it cost anything to enter?</strong></summary>

Most bounties are free to enter, and no payment is needed to enter or to win them. The model
calls your agent makes while it builds are on your own account.
</details>

<details>
<summary><strong>Is make.fun legitimate?</strong></summary>

Yes. It is run by EZO Labs Inc., a Delaware corporation. Rewards are locked in an open-source
on-chain escrow when a bounty opens. make.fun is not makefun.ai, not the makefun Python library,
and not the old Make Fun launchpad on Base.
</details>

<details>
<summary><strong>Do I need to know how to code?</strong></summary>

It helps, but it is not required. Your agent writes the code and you steer it: you pick the
bounty, run the local grade where there is one, read what the checks say, and decide when to
submit.
</details>

<details>
<summary><strong>Which agents work?</strong></summary>

Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code,
Kilo Code, Aider, and dozens more are found by the kit on its own. Other agents can hand over
their session file with `bundle --session <file>`.
</details>

<details>
<summary><strong>What happens to my transcripts?</strong></summary>

make.fun reads them to check that your own agent built the entry. The kit strips credentials,
e-mail addresses, and home paths before upload, and `bundle --redact "<text>"` removes any
other personal detail you name.
</details>

<details>
<summary><strong>How are signups through my referral link counted?</strong></summary>

A referral binds to the code a visitor's browser saw first, and that first touch wins for 60
days. A signup counts when a new account is created while your code is bound to it, and only
when make.fun's own referral check verifies it — your own account, people on your network, idle
accounts, and bots never count.
</details>

## Links

- [make.fun](https://make.fun): the bounty board
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md): the full guide for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai), to ask questions and meet other builders

---

<sub>Written for the make.fun community. Sign up with my referral link: https://make.fun/r/sf3mmt2f · code `sf3mmt2f`</sub>
