# Clojure Agent Starter

A starter for Clojure projects where an AI agent (Claude Code) is the primary
pair, working against a live REPL. Clone it, rename the example namespaces,
and start building.

## What's in the box

| Piece | Where |
|---|---|
| Paren-repair hook (PreToolUse) + clj-kondo-on-write (PostToolUse) | `.claude/settings.json`, `scripts/hook-clj-kondo.sh` |
| clj-kondo security ruleset (RCE/XXE var bans) | `.clj-kondo/config.edn` |
| Structured logging utility | `src/example/log.clj` |
| Spec patterns: envelope + payload multispec, boundary helpers | `src/example/specs.cljc` |
| Generated test data demo | `examples/spec_gen.clj` |
| REPL-tuned `diagnosing-bugs` skill (fork of [Matt Pocock's](https://github.com/mattpocock/skills)) | `.claude/skills/diagnosing-bugs/` |
| `style-review` skill + layered style guides (house guide, operative; community + Nubank guides vendored as fallback/reference) | `.claude/skills/style-review/`, `docs/style/` |
| Agent operating doctrine (write-first-then-eval, live-image discipline, image-drift checks) | `CLAUDE.md` |

## How the agent gets good at Clojure

Three layers:

1. **Mechanical guardrails** — the paren-repair hook and clj-kondo-on-write
   catch structural and semantic errors at the moment of writing.
2. **Layered style guides** — `docs/style/CLOJURE-STYLE.md` (operative)
   over the vendored community guide (fallback), wired into `CLAUDE.md` so
   the agent follows them and the `/style-review` skill can enforce them.
3. **Operating doctrine** — `CLAUDE.md`'s write-first-then-eval and
   live-image discipline, which make the agent *use* the REPL instead of
   working like it's in a compile-restart language.

## Quickstart

Prerequisites: JDK 21+, [Clojure CLI](https://clojure.org/guides/install_clojure),
[clj-kondo](https://github.com/clj-kondo/clj-kondo),
[Babashka](https://babashka.org) (the hooks and `clj-nrepl-eval` are bb scripts),
`jq` (used by the lint hook), and [bbin](https://github.com/babashka/bbin):

```bash
bbin install https://github.com/bhauman/clojure-mcp-light.git --tag v0.2.2
```

That one install provides both `clj-paren-repair-claude-hook` (the PreToolUse
hook) and `clj-nrepl-eval` (the REPL client the skills and CLAUDE.md use).

Then:

```bash
# see spec-generated test data
clojure -M examples/spec_gen.clj

# start an nREPL the agent can drive
clojure -M:nrepl
```

Open the repo in Claude Code and the hooks and skills are live: writes get
paren-repaired and cljfmt'd before they land, then linted by clj-kondo;
`/diagnosing-bugs` reaches for the running REPL before anything else.

## Starting a new project from this

- `CLAUDE.md` is the doctrine — keep it, and set your nREPL port and paths.
- `example.log` and `example.specs` are patterns, not a library: rename the
  namespaces to your project's and grow them in place.
- `docs/style/CLOJURE-STYLE.md` is the house guide — update the canonical
  alias table and the file-header rule for your project.
- The skills assume a live nREPL and `clj-nrepl-eval` (any nREPL CLI client
  works — adjust the commands inside the skills).
