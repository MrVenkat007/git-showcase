# 01 — Git Basics

**Taskly scenario:** You just decided to build a Todo app called Taskly. You create the first files locally and need to get it onto GitHub.

---

## The starting point

You've created two files on your machine:
```
taskly/
├── index.html   ← the app UI
└── tasks.js     ← the task logic
```

Now you need Git to track them and GitHub to host them.

---

## Side-by-side: command → what actually happens

---

### 1. `git init`

| You run | Git does |
|---------|----------|
| `git init` | Creates a hidden `.git/` folder. Your folder is now a Git repo. Nothing is tracked yet. |

```bash
$ git init
Initialized empty Git repository in /taskly/.git/
```

---

### 2. `git status`

| You run | Git does |
|---------|----------|
| `git status` | Shows every file Git can see but isn't tracking yet. |

```bash
$ git status

Untracked files:
        index.html
        tasks.js

nothing added to commit but untracked files present
```

> Git sees your files but is not saving their history yet.

---

### 3. `git add` — move files to the staging area

| You run | Git does |
|---------|----------|
| `git add index.html` | Stages only `index.html`. `tasks.js` stays untracked. |
| `git add .` | Stages **everything** in the current folder. |

```bash
$ git add .
$ git status

Changes to be committed:
        new file: index.html
        new file: tasks.js
```

> Files are now **staged** — sitting in a waiting room, ready to be committed.

---

### 4. `git commit` — take a permanent snapshot

| You run | Git does |
|---------|----------|
| `git commit -m "feat: initialise Taskly project"` | Saves a snapshot of staged files with your message. Local only — GitHub doesn't know yet. |

```bash
$ git commit -m "feat: initialise Taskly project"

[main (root-commit) 4f3a1c2] feat: initialise Taskly project
 2 files changed, 45 insertions(+)
```

💡 **Commit message format:** `type: short description`
| Type | When to use |
|------|-------------|
| `feat` | Adding a new feature |
| `fix` | Fixing a bug |
| `docs` | Documentation only |
| `chore` | Maintenance, config, cleanup |
| `refactor` | Restructuring without changing behaviour |

---

### 5. `git remote add` — link your local repo to GitHub

| You run | Git does |
|---------|----------|
| `git remote add origin https://github.com/you/taskly.git` | Saves "origin" as a shortcut name for your GitHub repo URL. |

```bash
$ git remote add origin https://github.com/you/taskly.git
$ git remote -v

origin  https://github.com/you/taskly.git (fetch)
origin  https://github.com/you/taskly.git (push)
```

---

### 6. `git push` — upload commits to GitHub

| You run | Git does |
|---------|----------|
| `git push -u origin main` | Uploads your local commits to GitHub. `-u` remembers this as the default so future pushes just need `git push`. |

```bash
$ git push -u origin main

Enumerating objects: 4, done.
Writing objects: 100% (4/4)
Branch 'main' set up to track 'origin/main'.
```

> Taskly is now live on GitHub. ✅

---

### 7. `git log` — see the commit history

| You run | Git does |
|---------|----------|
| `git log --oneline` | Shows every commit as one compact line. |

```bash
$ git log --oneline

4f3a1c2 feat: initialise Taskly project
```

---

### 8. `git pull` — get changes someone else pushed

**Scenario:** You edited `index.html` directly on GitHub (added a page title).
Now your local copy is one commit behind.

| You run | Git does |
|---------|----------|
| `git fetch origin` | Downloads changes from GitHub but does NOT apply them yet. Safe to inspect first. |
| `git pull origin main` | Downloads **and** immediately merges remote changes into your local branch. |

```bash
$ git pull origin main

remote: Enumerating objects: 5, done.
Updating 4f3a1c2..9b2e4f1
Fast-forwarded main to origin/main.
1 file changed, 3 insertions(+)
```

---

## Mental model — the four zones

```
 Working Directory → Staging Area → Local Repo → GitHub Remote
    (edit files)      (git add)    (git commit)   (git push)

                  ←————————————————————————————— git pull
```

Every change travels left → right.
`git pull` is the only one that goes right → left.

---

## Key insight

> `git add` and `git commit` are two steps on purpose.
> You might change 10 files but only want to commit 3 related ones together.
> The staging area gives you that precision.
