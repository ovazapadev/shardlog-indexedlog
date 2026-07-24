# Shardlog

**A modular changelog pattern that keeps project history fast to search — for humans and AI coding agents alike.**

[![Pattern](https://img.shields.io/badge/type-engineering%20pattern-blue)](#)
[![Stack](https://img.shields.io/badge/stack-agnostic-lightgrey)](#)
[![Status](https://img.shields.io/badge/status-battle--tested-brightgreen)](#)

One giant `CHANGELOG.md` or `PROJECT_LOG.md` that grows forever eventually becomes the most expensive file
in your repo to read — every "what did we do last week?" question costs you the *entire* history, not just
the answer. Shardlog fixes that by splitting the log along two axes — **module** and **date** — with a cheap
index at every level, so any question costs roughly what the answer is worth, not what the whole project's
history weighs.

Originally developed for keeping an AI coding assistant's session memory efficient on a long-running
production codebase, but the pattern itself has nothing to do with AI — it works the same way for a human
opening the repo at 9am asking "what happened in payments this week?"

## Table of contents

- [The problem](#the-problem)
- [The pattern](#the-pattern)
- [Why it works](#why-it-works)
- [Comparison](#comparison)
- [Getting started](#getting-started)
- [When *not* to use this](#when-not-to-use-this)
- [FAQ](#faq)

## The problem

A single running log file, appended to every session/sprint/day, has three compounding issues:

1. **Unbounded read cost.** To answer "what changed in module X yesterday?" you read the *entire* file,
   because there's no way to slice it without opening it first.
2. **Two concerns, one file.** *What happened and when* (a changelog) gets tangled with *why it happened and
   how it was designed* (architecture/decision notes) — over time these drift out of sync or get duplicated.
3. **No natural place to disambiguate.** "The billing module" might mean something different to the finance
   team than to the ops team — a flat log has nowhere to encode that distinction, so it gets guessed at
   query time, inconsistently.

## The pattern

Split by **module** (and **layer/role**, only where genuinely ambiguous) → then by **date**, with a
one-line index at every level:

```
log/
  _index.md                        ← map of every module, one line each
  <module>/
    _index.md                      ← every date touched in this module, one line each
    <YYYY-MM-DD>.md                ← full detail for that day, that module
    <layer>/                       ← ONLY if the module genuinely differs by role/audience
      _index.md
      <YYYY-MM-DD>.md
```

**Rule of thumb for `<layer>`:** open a layer subfolder only when the *same* module name would otherwise
mean two genuinely different things depending on who's asking (e.g. a "billing" module that means something
structurally different to Finance than to Ops). If there's no real ambiguity, skip the layer — go straight
to `<module>/<date>.md`.

### Design decisions live elsewhere, on purpose

Shardlog is a **changelog** — *what* happened, *when*. It deliberately does not hold *why* a feature works
the way it does, or the tradeoffs behind a design. That belongs in a separate, single canonical doc per
topic (an ADR, a design doc, whatever your team already uses). A Shardlog entry that closes out a design
just links to it:

```markdown
## 2026-07-23 — Rate limiter redesign shipped
Closed the design discussed in `docs/rate-limiter-design.md`. See that doc for the "why" — this entry
is just the "it shipped, here's what touched."
```

No duplication, no drift — one canonical source per decision, one changelog entry pointing at it.

## Why it works

| # | Benefit | What it actually buys you |
|---|---|---|
| 1 | **Read cost scales with the question, not the history** | "What happened in `checkout` yesterday?" costs one small index file, not the whole project's history — whether that history is 2 weeks or 2 years old. |
| 2 | **Never degrades** | Every date file is small and fixed in size. Only the index grows, and it grows by one line per day — it never becomes expensive to read. |
| 3 | **Zero information loss on reorganization** | Migrating into this shape moves content, it doesn't summarize it away. Keep the original file frozen as a backup until the migration is verified — never delete blind. |
| 4 | **No changelog/design drift** | Changelog and design docs are structurally separate, so there's exactly one place to update a decision — the changelog just links to it instead of copying it. |
| 5 | **Disambiguation is structural, not tribal knowledge** | When a module name is genuinely ambiguous across roles, the layer subfolder forces a "which one do you mean?" moment instead of silently mixing incompatible history. |
| 6 | **Stack-agnostic, tool-agnostic** | It's folders and Markdown. Works identically whether your project is Python, Java, Rust, or a spreadsheet — it describes how you organize *knowledge about the work*, not the work itself. |
| 7 | **Auditable with the dumbest possible tools** | No database, no proprietary format. `ls`, `grep`, and a text editor are a complete toolkit for reading, verifying, or reverting any part of it. |

### Why this matters extra for AI coding agents

LLM-based coding assistants pay for every token they read, every single time — including re-reading the
same growing log file at the start of every session just to "catch up." Shardlog turns that fixed,
ever-growing cost into a cost proportional to the actual question being asked (index file first, only open
a specific day's detail if it's actually needed). The same property that makes this pleasant for a human
skimming git history makes it materially cheaper for an agent re-establishing context every session.

## Comparison

| | Single running log | [Keep a Changelog](https://keepachangelog.com/) | Obsidian daily notes | **Shardlog** |
|---|---|---|---|---|
| Read cost for "what happened in X last week?" | Whole file | Whole file (per version, not per module) | Manual folder browsing | One small index file |
| Scales with project age | ❌ degrades | ❌ degrades | ⚠️ depends on vault structure | ✅ flat forever |
| Separates *what* from *why* | ❌ | ❌ (release notes only) | ⚠️ up to the user | ✅ by design |
| Module/domain granularity | ❌ | ❌ (version granularity) | ⚠️ up to the user | ✅ first-class |
| Tool dependency | None | None | Obsidian (soft dependency) | None |

Shardlog isn't a replacement for Keep a Changelog (that's about *releases*, this is about *work sessions*) —
they compose fine together.

## Getting started

1. **Name your modules/domains.** Doesn't need to be complete on day one — add them as they show up.
2. **Create `log/` with an empty `_index.md`.**
3. **Decide your trigger phrase.** A short, memorable command (`/log`, `*log`, whatever fits your workflow)
   that means "write today's entries now, no need to ask me what to include again" — agree on the meaning
   once, up front.
4. **From day one: design docs are separate from the changelog.** Retrofitting this split later means a
   migration; starting with it costs nothing.
5. **Add a layer subfolder only when a module actually becomes ambiguous** — don't pre-guess it.

## When *not* to use this

- **Short-lived or tiny projects** that will never accumulate enough history for a single file to become a
  real problem — the folder structure is overhead you don't get paid back for.
- **No selective-read tooling.** If whatever reads this log always has to open everything anyway, splitting
  files buys you nothing — the benefit is entirely in *not* reading what you don't need.

## FAQ

**Does this replace git history / commit messages?**
No. Git tells you *what changed in the code*. Shardlog tells you *what happened in a work session* —
decisions, context, things that didn't necessarily produce a single clean commit. They're complementary.

**What if an entry touches multiple modules?**
Write it once per module folder it genuinely belongs to, each with the detail relevant to that module. Don't
force one home for a session that spans multiple concerns.

**Can I automate index updates?**
Yes — a pre-commit hook or a small script that appends the one-line summary to `_index.md` when a new dated
file is added is a natural next step once the pattern feels right by hand.

---

Migrated from a 1,300+ line single-file project log without losing a single recorded fact — split into
per-module, per-date files with index files at every level, in one pass, with the original kept frozen as a
verified backup. That's the pattern working as intended.

🇪🇸 Versión en español: [`README.es.md`](README.es.md)
