# 03 — Merge Conflicts

**Taskly scenario:** You and a friend are both working on Taskly. You both edited the same function in `tasks.js` at the same time — on separate branches. When you try to merge, Git doesn't know which version to keep. That's a conflict.

---

## Why conflicts happen

Git is smart enough to merge changes automatically when they're in different parts of a file.
But when **two people edit the exact same lines**, Git stops and says:
*"I don't know which one you want — you decide."*

---

## Reproducing this conflict step by step

---

### Step 1: Both branches start from the same point

`tasks.js` has this function on `main`:
```javascript
function sortTasks(tasks) {
  return tasks;  // no sorting yet
}
```

---

### Step 2: You create `feature/sort-by-date` and change it

```bash
$ git switch -c feature/sort-by-date
```

You edit `tasks.js`:
```javascript
function sortTasks(tasks) {
  return tasks.sort((a, b) => new Date(a.date) - new Date(b.date));
}
```

```bash
$ git add tasks.js
$ git commit -m "feat: sort tasks by due date"
```

---

### Step 3: Your friend creates `feature/sort-by-priority` from the same `main` and changes the same line

```bash
$ git switch main
$ git switch -c feature/sort-by-priority
```

Friend edits `tasks.js`:
```javascript
function sortTasks(tasks) {
  return tasks.sort((a, b) => a.priority - b.priority);
}
```

```bash
$ git add tasks.js
$ git commit -m "feat: sort tasks by priority level"
```

---

### Step 4: Merge the first branch — no problem

```bash
$ git switch main
$ git merge feature/sort-by-date

Updating 4f3a1c2..8d2b1e3
Fast-forward
 tasks.js | 2 +-
```

> Clean. `main` now has the date-sorting version.

---

### Step 5: Merge the second branch — CONFLICT

```bash
$ git merge feature/sort-by-priority

Auto-merging tasks.js
CONFLICT (content): Merge conflict in tasks.js
Automatic merge failed; fix conflicts and then commit the result.
```

> Git found two different edits on the same line. It can't decide. You have to.

---

## Side-by-side: what Git put in the file

Git rewrites the conflicted file like this:

```javascript
function sortTasks(tasks) {
<<<<<<< HEAD
  return tasks.sort((a, b) => new Date(a.date) - new Date(b.date));
=======
  return tasks.sort((a, b) => a.priority - b.priority);
>>>>>>> feature/sort-by-priority
}
```

| Marker | Means |
|--------|-------|
| `<<<<<<< HEAD` | Start of YOUR version (what's on `main` right now) |
| `=======` | Divider between the two versions |
| `>>>>>>> feature/sort-by-priority` | End of the INCOMING version (what's being merged in) |

---

## Resolving it

**Scenario:** After talking with your friend, you agree — support both sort modes with a parameter.

You open the file and manually edit it to the final version you want:

```javascript
function sortTasks(tasks, mode = "date") {
  if (mode === "priority") {
    return tasks.sort((a, b) => a.priority - b.priority);
  }
  return tasks.sort((a, b) => new Date(a.date) - new Date(b.date));
}
```

> The conflict markers are completely gone. The file is valid code again.

---

### Then finish the merge

| You run | Git does |
|---------|----------|
| `git add tasks.js` | Tells Git "I've resolved this file." |
| `git commit` | Completes the merge with an auto-generated merge commit message. |

```bash
$ git add tasks.js
$ git commit

[main f9c3d7a] Merge branch 'feature/sort-by-priority'
```

---

## Checking for remaining conflicts

Before committing, always run:

```bash
$ git status

All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)
```

If other files are still conflicted, they'll show under `Unmerged paths`.

---

## Key insight

> A conflict is not an error — it's Git asking for a human decision.
> The resolution is just editing the file to what you *actually* want, then staging it.
> Once you've seen a few conflict markers, they stop being scary.
