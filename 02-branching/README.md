# 02 — Branching & Merging

**Taskly scenario:** Taskly is live. You want to add two new features — a **priority label** on tasks and a **dark mode toggle** — without breaking what's already working on `main`.

---

## Why branches exist

Without branches, every half-finished feature lives on `main`.
One bad change breaks everyone. Branches let you work in isolation
and only merge back when something is ready.

---

## Side-by-side: command → what actually happens

---

### 1. Creating a branch

**Scenario:** You're about to build the priority label feature. You don't want to touch `main` until it's done.

| You run | Git does |
|---------|----------|
| `git branch feature/priority-label` | Creates the branch. You're still on `main`. |
| `git switch feature/priority-label` | Moves you onto the new branch. |
| `git switch -c feature/priority-label` | Creates **and** switches in one step (shortcut). |

```bash
$ git switch -c feature/priority-label

Switched to a new branch 'feature/priority-label'
```

> `main` is untouched. All your changes now live only on `feature/priority-label`.

---

### 2. Working on the branch

You add priority badges to `index.html` and commit:

```bash
$ git add index.html
$ git commit -m "feat: add priority label to task cards"

[feature/priority-label 7c3d1a9] feat: add priority label to task cards
 1 file changed, 12 insertions(+)
```

Meanwhile, `main` still looks exactly like it did before you branched.

---

### 3. Fast-forward merge

**Scenario:** Priority label is done. `main` hasn't changed since you branched off.
Git can simply slide `main` forward to your latest commit — no merge commit needed.

| You run | Git does |
|---------|----------|
| `git switch main` | Move back to `main`. |
| `git merge feature/priority-label` | Fast-forwards `main` to point at your branch's latest commit. |

```bash
$ git switch main
$ git merge feature/priority-label

Updating 4f3a1c2..7c3d1a9
Fast-forward
 index.html | 12 ++++++++++++
```

```
Before:           After:
main              main
  ↓                 ↓
──●               ──●──●
       ↑                ↑
    feature           feature (merged)
```

---

### 4. Merge commit (`--no-ff`)

**Scenario:** Dark mode is done, but this time `main` has moved on (someone merged a hotfix while you were working). A fast-forward isn't possible — Git has to create a merge commit to combine the two lines of work.

You can also force this even when fast-forward is possible with `--no-ff`. Good teams often do this to keep a visible record of every feature merge.

| You run | Git does |
|---------|----------|
| `git merge --no-ff feature/dark-mode` | Creates a merge commit even if fast-forward is possible. Preserves the history of the branch. |

```bash
$ git switch main
$ git merge --no-ff feature/dark-mode

Merge made by the 'ort' strategy.
 tasks.js   | 18 ++++++++++++
 styles.css | 24 ++++++++++++++++
```

```
Before:                After:
main ──●──●            main ──●──●──────────●  ← merge commit
             \                         \      /
        feature ──●──●            feature ──●──●
```

---

### 5. Rebase — cleaner alternative to merge

**Scenario:** You've been on `feature/filter-tasks` for a few days. `main` has 3 new commits since you branched. Instead of merging (which creates a messy merge commit), you want to replay your work on top of the latest `main`.

| You run | Git does |
|---------|----------|
| `git switch feature/filter-tasks` | Move onto your feature branch. |
| `git rebase main` | Picks up your commits and replays them on top of the current `main`. |

```bash
$ git rebase main

Successfully rebased and updated refs/heads/feature/filter-tasks.
```

```
Before rebase:              After rebase:
main ──●──●──●              main ──●──●──●
        \                                  \
   feature ──●──●              feature ──●──●  (replayed on top)
```

Then merging is a clean fast-forward:
```bash
$ git switch main
$ git merge feature/filter-tasks    # clean, linear, no merge commit
```

---

## Merge vs Rebase — when to use which

| Situation | Use | Why |
|-----------|-----|-----|
| Merging a finished feature to `main` | **Merge (--no-ff)** | Preserves the branch history |
| Keeping your feature branch up to date | **Rebase** | Stays linear, easier to read |
| Branch has been shared with others | **Merge only** | Never rebase shared history — it rewrites commit hashes and breaks teammates' copies |

---

## Key insight

> Think of rebase as saying: "Pretend I started this feature from today's `main`, not last week's."
> Think of merge as saying: "Combine these two histories and record that it happened."
