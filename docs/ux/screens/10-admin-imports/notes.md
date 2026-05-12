# 10 · Admin — Imports & Level overrides

## Design intent

Two admin tools that share a single brief: **make destructive actions feel auditable, recoverable, and boring in the best sense**. The visual language inherits the Nordic discipline established on `LoginScreen` — Inter + Inter Tight, hairline borders, faint 56px grid backdrop, magenta reserved exclusively for the one primary CTA per page — and adds **JetBrains Mono** for any value that proves something happened: timestamps, row numbers, run IDs, email addresses. Mono = machine-truth; Inter = human prose.

## Imports

The two cards on `imports.html` are symmetrical so admins can scan "what's healthy" in one glance. Dry-run is **on by default**, rendered as a labelled switch in the card footer next to the primary CTA so it's impossible to ignore. Errors are surfaced three times — as a count tile, an inline expandable summary on the card, and in the global history table — each level deeper than the last. The history rows use status pills (`success` / `partial` / `dry-run` / `failed`) following the principle that status is icon + label, never colour alone.

## Imports error

`imports-error.html` opens with a soft diagonal alarm stripe (~6% red) plus a single octagon — calm, not panicky. The field-level error table prints `row · field · message · severity` with monospaced row numbers so admins can paste them straight into the sheet's "Go to row" box. A black server-log panel sits in the sidebar to anchor the failure in real telemetry, and a 4-step recovery checklist tells the admin exactly what to do next.

## Level override

The override flow makes auditability the visual hook: an explicit "audit-logged" pill in the header, a stippled signature stamp preview before submission showing **who · when**, and an append-only history timeline using the L1→L5 colour palette. Level chips reuse the same five colours as the login ladder. The current level is annotated `now` so the change is unmistakable. "Auto-expire on next survey" is the default — overrides should be hints to the system, not pins.
