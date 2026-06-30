# Issue & Roadmap Conventions — constructorfabric

> **DRAFT / PROPOSAL.** Agreed operating model, not yet applied to any repo or project.
> Org makes three products — **gears, studio, insight** — each its own set of repos and its own roadmap.

## 1. The unit model

```
area (label)  →  Feature  →  Task / Bug   (Feature → child via sub-issues)
```

- **Area** — a stable product area (e.g. `area:connectors`, `area:identity`). A **label**, not an issue. Groups the roadmap. Scoped per product. (`initiative:*` stays available but optional, only for time-bound pushes — not for stable areas.)
- **Feature** — a roadmap item / deliverable. Issue **type = Feature**.
- **Task / Bug** — granular work. Issue **type = Task / Bug**. Usually a **sub-issue** of a Feature, but may stand alone (e.g. a chore).

Only **three issue types**: `Feature`, `Task`, `Bug`. No Epic — an area label + sub-issues give the same grouping without another level.

`Type` and `parent` are **independent** properties. An issue is `Task` (its type) *and* a sub-issue of a Feature (its parent) — set both, separately.

### chore & spike
Both are just `Task` + a label:
- `chore` — upkeep with no direct user value (deps bump, CI, refactor).
- `spike` — research/PoC; output is a decision, not shippable code.

They may live outside any Feature. Give them an `area:*` label so they still show on the roadmap.
Open `spike` issues = the **open questions / research still in flight** — surfaced by a dedicated view (see §4), not by any forced time-box rule. A spike can simply sit in the backlog.

## 2. Statuses

| Issue | Status flow | Who moves it |
|---|---|---|
| **Feature** | `Not started → In Spec → In Dev → In Review → Done` | feature owner / lead |
| **Task / Bug** | `Todo → In progress → Done` | the assignee |

Status = **which phase**. It is set by a human and is coarse on purpose.

**Progress** ("how much is done") is NOT a manual number — it is the GitHub **Sub-issues progress bar** (closed ÷ total children), computed automatically. Add scope (e.g. +4 tasks) and the bar drops honestly on its own; a manual % would silently lie.

## 3. Gates (soft) + deviation views

Each Feature carries a **Gates** checklist (see the Feature template):

- [ ] Requirements approved — *PO / owner*
- [ ] Architecture review passed — *architect, or mark N/A (e.g. pure GUI)*
- [ ] QA passed — *QA*

Gates are a **convention**, not enforced by GitHub (anyone with write can tick a box; Projects can't block a status transition). We **detect, not prevent**, via 🚩 saved views on the roadmap:

- 🚩 `In Dev`+ but **Requirements** unchecked
- 🚩 `In review`/`Done` but **Architecture** unchecked (and not N/A)
- 🚩 `Done` but **QA** unchecked
- 🚩 `Target date` < today and Status ≠ `Done` (schedule slip)

> Future hardening: the **Architecture** gate can become a real, identity-enforced approval by making the design an ADR/design-doc **PR** with `CODEOWNERS → @constructorfabric/architects` + required review. "Arch passed" then = that PR merged.

## 4. Roadmap fields (Project)

Per-product roadmap Project (Gears / Studio / Insight Roadmap) + one read-only Portfolio across all three. Fields:

- **Status** — the Feature flow above.
- **Commitment** — `Committed` / `Candidate` (later maybe `Stretch`). Separates promises from ideas. 🚩 view: `Committed` without a milestone.
- **Target date** / **Milestone** — see cadence.
- **Design PR** — link to the design/ADR PR (the arch gate artifact).
- Standard: Sub-issues progress, Linked pull requests, Parent issue, Labels, Repository.

Recommended views: *By Area*, *By Milestone*, *Features + progress* (`type:Feature`), *Open spikes* (open issues with label `spike` = research / open questions still in flight), and the 🚩 deviation views.

## 5. Cadence & milestones

- **Milestone `YY.MM`** (e.g. `26.02` = "done by end of Feb 2026") is the single cadence carrier and the release anchor.
- Milestones are **repo-level** — `26.02` must exist in every repo of a product; kept in sync by a script.
- **Phase** is an *optional* label (`mvp`, `ga`, …) — a coarse arc over months. May be empty.
- (Iteration field deferred — add only if intra-month sprints appear, e.g. `26.02-I`.)

## 6. Severity & other axes

- **Severity** (bugs only): one label, `severity:blocker` / `critical` / `major` / `minor` — *how bad the impact is*.
- **When** to fix is **not** a separate label — it's the **milestone** the issue is assigned to. (No `priority:*` axis; scheduling = milestone.)
- Keep existing **`team:*`** (owning team) and **`component:*`** (code area) labels.

## 7. PRs ↔ issues

A PR is a code change, an issue is a work item — different objects, linked, never converted. They only share a numbering sequence, which is why they get confused.

| | **Issue** | **Pull Request** |
|---|---|---|
| What | a unit of work ("what to do") | a proposed **code change** (commits on a branch) |
| Holds | description, type, labels, sub-issues | diff, review, CI checks, Merge button |
| Lifecycle | open → closed | open → merged/closed |

Flow — a PR closes a **Task**, which rolls up to its **Feature**:

```
PR  ──Closes #312──►  Task #312  ──(sub-issue of)──►  Feature #300
       merge PR  ─►  Task auto-closes  ─►  Feature progress bar advances
```

- In a PR description: `Closes #<task>` (or `Fixes`/`Resolves`). On merge the **task** auto-closes and the bar advances. A PR usually closes a **Task**, not the Feature. The Feature is closed manually once all its sub-issues are done (no auto-close for the Feature).
- Label the **PR** itself (`feature` / `bug` / `chore`) — this drives **Release Drafter** categories. Issue *type* does **not** propagate to the PR.

## 8. Releases

Two layers:
1. **Per-repo technical release** — tag + GitHub Release + artifacts to Artifactory, on milestone close.
2. **Product rollup** — generated from all `milestone:YY.MM` issues across the product's repos → a single "Insight 26.02" note.

- **Versioning**: products **CalVer `YY.MM`**; shared libraries / SDK / crates **SemVer** (released independently).
- **Release notes**: **Release Drafter** — a draft accumulates as labelled PRs merge; finalized at release.
- **Cadence**: monthly, anchored to the milestone close.
