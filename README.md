# skill-story

**Interaction flows are product surface. Test them like code.**

Agent skills are usually described by their features — but for many skills the
real product is a *conversation*: an onboarding interview, an adversarial
reviewer that keeps pushing back, a confirm-gated pipeline. Prompts are black
boxes; you cannot tell whether the flow actually runs the way the SKILL.md
intends, and hand-testing every prompt change does not scale.

skill-story is a meta-skill that turns any skill's interaction flow into a
**visible, repeatable, verifiable story**:

```
dive in → expected script → sandboxed REAL session → drive & capture →
verify outcome → findings → sediment into the target skill → re-record
```

<p align="center">
  <a href="docs/skill-story-demo.mp4">
    <img src="docs/skill-story-demo.gif" alt="A real onboarding session — AskUserQuestion batches, PDF import, consents — captured and re-rendered with true colors" width="880">
  </a>
  <br><em>A real onboarding session end to end (7× speed) — <a href="docs/skill-story-demo.mp4">full-quality video</a>.</em>
</p>

## How it works

1. **Dive in — script first** (the way you'd storyboard a product video):
   read the target skill, write `story.md` with the expected stages, the
   questions the user should be asked, and the state each stage must produce.
   That document is the test spec.
2. **Record a REAL session**: `node scripts/story-record.mjs <name> --kickoff
   <prompt-file>` starts the actual agent CLI in tmux (kickoff must point the
   skill at a sandbox data home) and snapshots the terminal — with ANSI
   colors — into `skill-stories/<name>/frames.json`. The recorder only
   records; a human (`tmux attach`) or an agent (`tmux send-keys`, debounced)
   drives the answers.
3. **Verify mechanically**: assert the sandbox files/state the spec demanded;
   classify every deviation (prompt gap / behavior bug / spec wrong / driver
   ergonomics) and sediment it. Never fix a finding by silently weakening the
   spec.
4. **Render — capture once, render many**: `node scripts/story-render.mjs
   <name>` re-renders the frames into a self-contained animated `replay.html`
   and a `story.mp4` (SGR span parser: 16/256/truecolor). Restyle and re-cut
   without ever re-running the session.

Each story directory (spec + frames + findings) is one entry in the target
skill's **interaction test set** — re-record after prompt changes and diff
against the spec: that's the regression test for prompt work.

## Requirements

- `tmux` (capture), Node 18+
- macOS `qlmanage` + `ffmpeg` for mp4 rendering (skipped gracefully elsewhere;
  `replay.html` needs nothing)

## Install as a skill

Drop this directory into your agent's skills tree (e.g.
`~/.claude/skills/skill-story/` or your repo's `.agents/skills/`), or use it
directly from a clone.

Born inside [CoForce Apply](https://github.com/Sma1lboy/coforce-apply), where
its first stories caught real prompt bugs (a sandbox-boundary leak, an
invented repo URL, a half-empty resume page) before any user did.

MIT © 2026 Sma1lboy
