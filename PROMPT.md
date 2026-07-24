# Bootstrap prompt

Copy the block below and paste it to your AI coding assistant (Claude, ChatGPT, Cursor, or any other) at
the start of a new project, or any time you want it to start using the Shardlog pattern. It's plain
instructions — no tool-specific syntax, works anywhere. Full explanation of the pattern: [`README.md`](README.md).

---

```
Starting now, organize project memory/history using the "Shardlog" pattern instead of one growing log file.
Full spec: https://github.com/ovazapadev/shardlog-indexedlog

RULES

1. Structure: a top-level `log/` folder (or whatever root name I confirm with you), containing one
   subfolder per module/domain of this project. Inside each module folder:
   - `_index.md` — one line per dated entry that exists in this module (date + one-sentence summary).
   - `<YYYY-MM-DD>.md` — one file per calendar day of work in that module, with full detail.
   - A `<layer>/` subfolder ONLY if this module genuinely has different content depending on role/audience
     (e.g. the same module means something structurally different to two different user types). Don't
     pre-create layers "just in case" — only when a real conflict shows up.
   Also keep one root `log/_index.md` listing every module that has any activity, one line each.

2. Separate changelog from design. This structure is for CHANGELOG entries only — what happened, when,
   which files/commits were touched, what broke and how it was fixed. Anything that's a DESIGN DECISION
   (why something works the way it does, tradeoffs considered, architecture) goes in its own canonical doc
   per topic, wherever this project already keeps that kind of documentation. A changelog entry that closes
   out a design just links to that doc — never copy the design into the changelog.

3. Before creating a new module folder, ask me to confirm the module list, or infer it from the project's
   existing structure if that's unambiguous — don't invent unnecessary categories.

4. Never delete or bulk-rewrite an existing single-file project log to adopt this pattern. If one already
   exists, migrate its content into this structure preserving every concrete fact (file names, commands run,
   decisions, bugs and fixes) — freeze the original as a read-only backup, don't remove it until I confirm
   the migration is complete and correct.

5. Agree with me on a short trigger phrase (I'll tell you what I want to use, e.g. "*log" or "/log") that
   means: "write today's entries now, across whatever modules had activity this session, without asking me
   again what to include."

6. When I ask things like "what did you do yesterday/this week [in module X]", answer using only the
   relevant `_index.md` file(s) — filtered by date — and only open a specific day's detail file if I ask for
   more depth. Never re-read the old single-file log or unrelated modules to answer this.

7. If a module name could mean genuinely different things depending on who's asking, ask me which one I mean
   before assuming — don't silently guess and mix incompatible history under one folder.

Confirm you understood this, ask me for the initial module list (or propose one based on the project), and
ask me what trigger phrase I want to use before creating anything.
```

---

## Notes

- This prompt is intentionally verbose and explicit — paste it once per project, not once per session. Most
  assistants will remember it for the rest of that conversation/session; for persistent memory across
  sessions, save it wherever your assistant's memory/instructions file lives (e.g. a `CLAUDE.md`, a system
  prompt, a project rules file).
- The trigger phrase (rule 5) is the piece that makes this sustainable day to day — agree on it once, reuse
  forever, no need to re-explain what "update the log" means each time.
- 🇪🇸 Versión en español: [`PROMPT.es.md`](PROMPT.es.md)
