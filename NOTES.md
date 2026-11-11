# RMS-web: design notes

Written 2026-11-11 from the owner's discussion in the LLVM-regsw session of 2026-11-09 … 11. Nothing is built; nothing here is decided beyond what the owner said.

## 1. What the owner asked for

- One place that shows **all progress of the project while we work**, for any frontend client.
- Per thread: **which shell scripts / commands are running**, with a **small live preview** of their output that can be opened on demand.
- The full registry and history: which build came from which thread, milestones, experiments.
- Later: a **WebGPU** UI. Note: a normal web page is enough for lists, logs and progress; WebGPU (the browser's graphics-card interface) pays off for heavy visuals, e.g. register-usage heatmaps or long traces, as a later extension inside the same UI.

## 2. Principles

1. **No state of its own.** Everything comes from RMS-manager's API; the web app never reads project files or git directly and never writes them.
2. **Read-only first.** Actions (approve a plan, start a run, free a stale lock, confirm a removal) come later and go through the manager, which commits through git. Removals always need the owner's explicit confirmation.
3. **Works on a phone** (the owner follows sessions remotely): narrow layout first.
4. **Live where it matters:** job list and console previews update live (WebSocket or server-sent events); the rest refreshes on demand.
5. **Local by default:** served on the machine (or the local network); no public hosting, no project data sent elsewhere.

## 3. Views (draft)

| view | shows |
|---|---|
| overview | reference system (latest milestone) and its headline results; open threads with status, lock holder and "last seen"; running jobs count; disk use per disk with the 85% warning |
| thread | goal, State, How to resume, open items (from THREAD.md); history from the commits; its experiments; its builds; its source repos and whether they moved; lock |
| jobs / consoles | running and recently finished commands per thread: command, PID, folder, start, CPU/memory, exit code; click → live console preview (tail of `store/consoles/…/<job>.log`) |
| experiments | list per thread; one experiment: record, config, `summary.csv` as table and chart, links to logs |
| builds | registry: name, thread, source commits, recipe, size, cost, pinned; provenance graph build → thread → commits |
| milestones | M1, M2, …: what merged, parts and versions, tags, results |
| rules | the rule book; which rules are enforced by tools; recent check failures |
| housekeeping | removal candidates and archive state (owner confirms in the UI) |

## 4. Stages

1. Wait for RMS-manager's read API (its stage 3); meanwhile mock data from LLVM-regsw's current files to shape the views.
2. Overview, thread, jobs/consoles (read-only, live).
3. Experiments (tables, charts), builds and provenance, milestones.
4. Actions through the manager.
5. Optional WebGPU visualisations.

## 5. Open questions

- Stack: plain TypeScript + a small build tool, or a framework (React, Svelte, …)? Charts library?
- Served by RMS-manager itself, or separately?
- Access control when reachable from the phone (local network only? a token?).
- Live channel: WebSocket or server-sent events.
- Name: "RMS" as given by the owner (meaning to be written down).
