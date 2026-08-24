# Notes

Working notes for this repo: status, decisions, and the traps that have actually bitten.
Migrated out of Claude Code's memory on 2026-08-24, so they are written in the first
person and dated by when each thing was learned — that date is usually the useful part.

Cross-cutting notes that are not specific to this repo live in
[fleet-notes](https://github.com/stoatworks-labs/fleet-notes).

*caspar-AV — live-events media server built on CasparCG (Rust bridge daemon + React console), ~/Projects/caspar-av, GitHub PUBLIC*

**caspar-AV** — a live-events media server (Pixera/disguise/Millumin-style) built on top of
CasparCG Server as the render engine, rather than a web AMCP client. Started 2026-07-28.
~/Projects/caspar-av, GitHub **PUBLIC**, MIT.

Forced architecture: CasparCG speaks AMCP over raw TCP 5250 and *pushes* state as OSC over
UDP — a browser can do neither — so it is a **Rust bridge daemon + React console**, the same
snapshot-mirror shape as [openstage](https://github.com/stoatworks-labs/openstage/blob/main/docs/NOTES.md) (`openstage`)'s orchestrator + console (whose shell,
connection layer with polling fallback, inspector idiom and palette were ported directly).

Four crates: `amcp` (codec + async client), `casparosc` (OSC + telemetry state tree),
`scanner` (media-scanner HTTP client), `showd` → binary `caspar-avd`.

The show layer is what makes it a media server: **screens** compile to `MIXER FILL` +
`MIXER PERSPECTIVE` (real corner-pin keystone), **cues** compile to `BEGIN`/`COMMIT` batches
so a multi-screen change lands on one frame. Six console pages: Media, Screens, Channels,
Cues, Templates, Grid.

69 Rust tests, zero clippy warnings. Verified end-to-end in a browser against
`scripts/fake-caspar.py` (speaks real AMCP framing + pushes real OSC). **Never run against a
real CasparCG server** — README and docs/scope.md say so. See [casparcg amcp](https://github.com/stoatworks-labs/fleet-notes/blob/main/notes/reference_casparcg_amcp.md).
