# Why a studio for embodied agents

## The gap

Embodied agent work rarely lives in one place. A typical debugging or evaluation cycle spans terminals for launching runners, simulator windows or saved frames, JSONL event traces, plain-text logs, videos, and spreadsheets assembled afterward to compare runs.

Across multiple rounds, that fragmentation breaks traceability. It becomes hard to see what changed between two versions, which environment checkout and Python interpreter produced a score, or whether a gain came from fixing failures rather than dropping unscored trials. When failures, interruptions, and exact inputs are scattered, iterative improvement loops are hard to see, compare, and trust.

## What the studio is

`visual RSI studio` (renamed from `deepseek-harness-LinkMind`, while `LinkMind` stays the name of the separate research method) is a visual workbench for embodied agents organized around four commitments:

- **Visualization**: inspecting real control sessions—agent messages, tool calls, and results—alongside camera snapshots, while tracking multi-round changes, metrics, and branches in the Agent RSI Board.
- **Data management**: storing each run's launch configuration, Git `HEAD`, tracked diff, hashes of non-ignored untracked files, logs, camera frames, and raw outputs, keeping failed and interrupted runs searchable and comparable across sessions.
- **Environment registration**: registering local simulation or real-world checkouts with explicit entry points, Python interpreters, dependencies, and asset checks so different methods can reuse one environment definition.
- **Measured real effect**: checking task completion, scores, and execution time under comparable conditions—keeping workbench usability separate from algorithm performance, never fabricating missing metrics, and never equating a zero exit code with success.

At the center is the **Agent RSI Board**, which visualizes the recursive self-improvement (RSI) and iterative evolution loops used by workflows such as RRSI and OpenRSI. Its evolution flow traces each round concretely: evidence under inspection, parallel candidate proposals, candidate review (such as leakage checks or rejecting hardcoded inputs), pluggable verification via local commands (for example, linters or formal builds where required checks gate evaluation), evaluation with planned trial counts and native reports, frontier adoption, and verified next-round loading—shown only when the next round's working tree `HEAD` matches the adopted commit.

A local example (`rsi-workflow-demo`) exercises this loop on eight Python tests across three candidates without a model or robot. Beside the live flow, a batch evaluation view displays a task-by-version matrix separating fixes, regressions, and newly scored cases—illustrated with bundled RoboDojo history (8 tasks, 400 scenarios: 385/400 scored initially, rising to 398/400 after filling 13 unscored scenarios) and a five-level Push-T tool ablation.

## Plugin versus studio

It helps to contrast `visual RSI studio` with `dsh-robotics`. `dsh-robotics` is intentionally a plugin that brings Google DeepMind's Antigravity Robotics workflows—such as a live MuJoCo Studio beside the chat—into stock DeepSeek Harness.

Today, `visual RSI studio` also ships as an unofficial community plugin on DeepSeek Harness (`DSH`). Reusing DSH's native chat, model picker, permissions, workspace selector, and file viewer enables a stacked workbench layout: files, control sessions, cameras, project overview, task graph, and the RSI Board sit in upper tabs, while the native conversation and terminal sit below.

Still, a plugin inside a coding harness is a starting point rather than the end goal. In a general coding harness, text files and chat turns are primary while environments, cameras, and multi-round lineages remain peripheral. The destination for `visual RSI studio` is a standalone application closer in spirit to Google's Antigravity—an agent-first IDE where the robot, its environment, and the improvement loop are first-class objects.

## Current status and boundaries

The current release is developer preview `0.12.0`, validated on **Linux x64 + DSH `0.2.2-rc.2`** (requiring Python 3.11+ with `venv` and Git). Windows and macOS are not supported because process supervision uses Linux `/proc`.

Built-in adapters cover four simulators alongside a `studio.environment.json` manifest for custom environments:

- **Verified for scene reset and RGB frame capture**: `LIBERO` (`OffScreenRenderEnv`, `libero_spatial`) and `LIBERO-Pro` (`libero_spatial_with_mug` with perturbed object assets).
- **Entry-point and configuration checked only**: `RoboCasa` and `RoboTwin 2`, which lacked full local dependencies and simulation assets on the test machine and have not yet produced native frames.

Just as important are the non-claims. The studio is not a new control algorithm, does not alter an imported method's policy, and does not automatically reproduce arbitrary repositories—weights, assets, GPUs, and setup steps remain explicit. Today, only one managed run executes at a time, closing DSH stops owned runs, provenance records hashes and diffs rather than full environment bundles, external control viewing is read-only unless a runner exposes a steering interface, and stopping a process is not a hardware emergency stop.

## Where it is going

Near-term work aims to complete the daily workflow—environment registration, launch, live observation (with mid-run steering where adapters allow it), fuller data archiving and handoff beyond hashes, and comparable evaluation—while finishing full simulation verification for `RoboCasa` and `RoboTwin 2`.

Over a longer horizon, the design notes outline decoupling simulation and compute jobs from the UI window via an independent supervisor, bounded background continuation with explicit budgets and resource leases, and project export with synchronized replay. Gradually, these steps are intended to carry `visual RSI studio` from a DSH preview toward a standalone studio for embodied agents.

[Back to README](../README.md)
