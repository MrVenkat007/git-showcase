# 04 — Advanced Git

**Taskly scenario:** Real-world Git situations that don't fit neatly into "add, commit, push." These are the tools that save you when things get complicated — or when you accidentally break something.

---

## Side-by-side: command → what actually happens

---

### 1. `git stash` — drop everything and come back later

**Scenario:** You're halfway through building a "recurring tasks" feature on `feature/recurring`.
Your teammate messages: *"Urgent! The delete button is broken on main — can you fix it?"*

You can't commit half-finished work. You can't just abandon it. `git stash` saves it temporarily.

| You run | Git does |
|---------|----------|
| `git stash` | Takes all your uncommitted changes and stacks them away. Your working directory goes back to the last clean commit. |
| `git stash list` | Shows all your saved stashes. |
| `git stash pop` | Re-applies the most recent stash and removes it from the stack. |

```bash
# Mid-feature, messy working directory
$ git stash

Saved working directory and index state WIP on feature/recurring: 3a1c2d4 feat: start recurring task UI

# Working directory is now clean — safe to switch branches
$ git switch main
$ git switch -c fix/delete-button
# ... fix the bug, commit, push ...

# Come back and continue where you left off
$ git switch feature/recurring
$ git stash pop

On branch feature/recurring
Changes not staged for commit:
        modified: tasks.js
        modified: index.html
```

> Your half-finished work is right back where you left it.

---

### 2. `git cherry-pick` — grab one specific commit from another branch

**Scenario:** You fixed a nasty bug (a task getting duplicated on refresh) on `feature/dedup-fix`.
But that branch isn't ready to merge — it has other unfinished changes.
You just want that one bug fix on `main` right now.

| You run | Git does |
|---------|----------|
| `git log --oneline feature/dedup-fix` | Shows the commits on that branch — find the one you want. |
| `git cherry-pick a1b2c3d` | Copies that single commit and applies it to your current branch. |

```bash
$ git switch feature/dedup-fix
$ git log --oneline

a1b2c3d fix: prevent task duplication on page refresh   ← want this one
9f4c2b1 feat: start dedup detection algorithm           ← not ready yet
3a1c2d4 feat: begin dedup feature branch

$ git switch main
$ git cherry-pick a1b2c3d

[main 7d3e9a2] fix: prevent task duplication on page refresh
 1 file changed, 6 insertions(+), 1 deletion(-)
```

> Just that one fix. Nothing else from the branch touched `main`.

---

### 3. `git tag` — mark a release

**Scenario:** Taskly has its first working version. You want to mark this point in history as `v1.0` so you can always come back to it.

| You run | Git does |
|---------|----------|
| `git tag v1.0` | Creates a lightweight tag (just a pointer to a commit). |
| `git tag -a v1.0 -m "First public release"` | Creates an **annotated** tag with a message, author, and date stored. Preferred for releases. |
| `git push origin v1.0` | Pushes the tag to GitHub (tags don't push automatically). |

```bash
$ git tag -a v1.0 -m "First public release of Taskly"
$ git push origin v1.0

Total 1 (delta 0), reused 0 (delta 0)
 * [new tag]         v1.0 -> v1.0
```

To see all tags:
```bash
$ git tag

v1.0
```

To check out an old tag (read-only):
```bash
$ git checkout v1.0
```

---

### 4. `git log` tricks — reading history like a pro

**Scenario:** You're trying to figure out who changed the task deletion logic and when.

| You run | What you get |
|---------|-------------|
| `git log --oneline` | Compact one-line history |
| `git log --oneline --graph --all` | Visual branch tree of the entire repo |
| `git log --author="Your Name"` | Commits by one specific person |
| `git log --since="1 week ago"` | Everything committed in the last week |
| `git diff main..feature/dark-mode` | Exact line-by-line diff between two branches |

```bash
$ git log --oneline --graph --all

* 7d3e9a2 (HEAD -> main) fix: prevent task duplication
* f9c3d7a Merge branch 'feature/sort-by-priority'
|\
| * b4a2c1e feat: sort tasks by priority level
|/
* 8d2b1e3 feat: sort tasks by due date
* 4f3a1c2 feat: initialise Taskly project
```

> You can see the entire branch and merge history at a glance.

---

### 5. `git reflog` — the ultimate undo button

**Scenario:** You ran `git reset --hard` trying to undo something and accidentally wiped out three good commits. The branch history looks wrong. Your work seems gone.

`git reflog` records **every time HEAD moved** — including resets, rebases, and checkouts.
It's your emergency escape hatch.

| You run | Git does |
|---------|----------|
| `git reflog` | Shows a full log of everywhere HEAD has been, in reverse order. |
| `git checkout HEAD@{3}` | Goes back to where HEAD was 3 moves ago. |
| `git branch recovered HEAD@{2}` | Creates a new branch from a previous HEAD position — recovers your lost commits. |

```bash
$ git reflog

7d3e9a2 HEAD@{0}: reset: moving to HEAD~3   ← the accidental reset
f9c3d7a HEAD@{1}: commit: Merge branch ...
b4a2c1e HEAD@{2}: commit: feat: sort by priority
8d2b1e3 HEAD@{3}: commit: feat: sort by date    ← your work is still here

# Recover it:
$ git branch recovered-work HEAD@{3}
$ git switch recovered-work
```

> Nothing is truly lost in Git until you run `git gc`. Reflog keeps everything for at least 30 days.

---

## Key insight

> `git stash` is for "not now."
> `git cherry-pick` is for "just this."
> `git tag` is for "remember this moment."
> `git reflog` is for "I didn't mean that."
