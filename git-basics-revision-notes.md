# Git Basics — Revision Notes

> Beginner-friendly notes for revising Git concepts and commands.
>
> **Learning status:** Basics covered through staging, commits, diffs, inspecting history, and restoring changes.

## 1. What is Git?

Git is a **version control system**. It records changes to files over time so you can review previous versions and understand how a project changed.

Think of Git as a series of saved snapshots of your project. Git does not automatically track every file or every edit on your computer: you choose what to stage and save in commits.

## 2. Git vs GitHub

- **Git** is the version control tool that runs on your computer.
- **GitHub** is an online service for hosting Git repositories and collaborating with others.

So far, our lessons have focused on Git in the Ubuntu VM terminal.

## 3. Check Git and configure your identity

Check the installed version:

```bash
git --version
```

Set the name and email Git records in commits:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Check the configuration:

```bash
git config --global --list
```

Use the name and email you actually want associated with your commits.

## 4. Basic terminal commands

| Command | Purpose |
|---|---|
| `pwd` | Shows the current directory. |
| `ls` | Lists visible files and directories. |
| `ls -la` | Lists files, including hidden files such as `.git`. |
| `cd /path/to/directory` | Changes to a directory. |
| `mkdir project-name` | Creates a directory. |
| `cat hello.txt` | Prints a file's contents. |
| `echo "A line" >> hello.txt` | Appends a line to a file. |

**Important:** `>>` appends to a file. A single `>` can overwrite the file's existing contents, so use it carefully.

## 5. Create or inspect a Git repository

Move into the practice repository:

```bash
cd /home/ubuntu/git-practice
```

Show the current directory:

```bash
pwd
```

Initialize Git in the current directory (normally done once when creating a new repository):

```bash
git init
```

Check the repository root:

```bash
git rev-parse --show-toplevel
```

Check the repository's state:

```bash
git status
```

Git stores repository metadata in the hidden `.git` directory.

## 6. The three important areas

The central mental model is:

**Working directory → Staging area → Commit history**

### Working directory
The actual files you view and edit.

### Staging area (also called the index)
The changes you have selected for the next commit.

### Commit history
The snapshots you have already saved using commits.

### The normal workflow

```bash
# Edit a file, then stage its current contents
git add hello.txt

# Save the staged changes in history
git commit -m "Describe the change"
```

`git add` stages the file's contents as they exist **when you run the command**. If you edit the file again afterward, those later edits are not automatically staged.

## 7. Create and track a file

Create a file by writing a line to it:

```bash
echo "Hello, Git!" > hello.txt
```

Read it:

```bash
cat hello.txt
```

Check Git's view of the repository:

```bash
git status
```

If the file is new and has not been staged, Git usually reports it as **untracked**.

Stage it:

```bash
git add hello.txt
```

Check again:

```bash
git status
```

Commit the staged contents:

```bash
git commit -m "Add hello.txt"
```

A commit message should briefly describe the change.

## 8. Inspect commits and history

Show a compact history:

```bash
git log --oneline
```

Show the latest commit and its changes:

```bash
git show
```

Inspect a particular commit by its ID:

```bash
git show <commit-id>
```

A commit ID identifies a particular saved snapshot. `HEAD` normally refers to the commit currently checked out.

## 9. Understand `git diff`

### See changes that are not staged

```bash
git diff
```

This compares the working directory with the staging area. It shows changes in the working file that have not been staged.

### See changes that are staged

```bash
git diff --staged
```

This compares the staging area with the latest commit (`HEAD`). It shows what is currently prepared for the next commit.

### Example

Suppose you run:

```bash
echo "Practising what gets committed." >> hello.txt
git add hello.txt
echo "This line is not staged yet." >> hello.txt
```

Now run:

```bash
git status
git diff
git diff --staged
```

Expected idea:

- `git status` can show `hello.txt` under both **Changes to be committed** and **Changes not staged for commit**.
- `git diff` shows `This line is not staged yet.` because that line was added after the last `git add`.
- `git diff --staged` shows `Practising what gets committed.` because that line was staged.

One file can have both staged and unstaged changes at the same time.

## 10. What gets included in a commit?

A commit records the **staged contents**, not every edit in the working directory.

Example:

```bash
echo "I am learning Linux." >> hello.txt
git add hello.txt
echo "I am learning Docker." >> hello.txt
git commit -m "Practise staged changes"
```

The commit includes the Linux line if it was staged successfully before the Docker line was added. The Docker line remains in the working file but is not included in that commit.

Check the result with:

```bash
git status
cat hello.txt
git show --stat --oneline HEAD
```

If a file has both staged and unstaged changes, committing saves the staged version. The later unstaged edit remains in your working file.

## 11. Restore vs unstage

These commands are different. Read carefully before using them because one can discard edits.

### Discard unstaged changes in a file

```bash
git restore hello.txt
```

By default, this restores the working file from the staging area. It discards unstaged edits to that file.

- If the file has staged changes and additional unstaged changes, the staged version is restored.
- If there are no staged changes for the file, it normally restores the file to its `HEAD` version.

**Warning:** This can permanently discard unstaged work. Inspect first:

```bash
git status
git diff
```

### Unstage changes but keep them in the file

```bash
git restore --staged hello.txt
```

This removes the file's changes from the staging area while keeping the edits in the working file. They become unstaged changes.

This command does not commit or delete those edits.

### Quick comparison

| Command | Main effect | Are the edits kept in the working file? |
|---|---|---|
| `git restore hello.txt` | Discards unstaged edits by restoring from the staging area | Unstaged edits are discarded; staged contents are preserved |
| `git restore --staged hello.txt` | Unstages changes | Yes, the edits remain in the file |

## 12. Useful status terms

| Status | Meaning |
|---|---|
| **Untracked** | Git is not yet tracking this new file. |
| **Changes to be committed** | Changes are staged for the next commit. |
| **Changes not staged for commit** | The tracked file has edits that are not staged. |
| **Working tree clean** | `git status` reports no current tracked changes or untracked files. |

## 13. A safe inspection routine

Before staging or discarding anything, use:

```bash
git status
git diff
git diff --staged
```

These help answer three questions:

1. What is the overall state?
2. What edits are not staged?
3. What edits are staged for the next commit?

After a commit, check:

```bash
git status
git log --oneline
```

## 14. Command cheat sheet

```bash
git --version                  # Check Git version
git status                     # Check repository state
git add hello.txt              # Stage current contents of a file
git commit -m "Message"        # Commit staged changes
git log --oneline              # Compact commit history
git show                       # Inspect latest commit
git show <commit-id>           # Inspect a specific commit
git diff                       # Show unstaged changes
git diff --staged              # Show staged changes
git restore hello.txt          # Discard unstaged edits in the file
git restore --staged hello.txt # Unstage edits but keep them in the file
```

## 15. Self-check questions

Try answering these from memory:

1. What is the difference between Git and GitHub?
2. What are the working directory, staging area, and commit history?
3. What does `git add hello.txt` do?
4. If you edit a file after running `git add`, are the new edits staged automatically?
5. What is the difference between `git diff` and `git diff --staged`?
6. Does a commit include unstaged edits?
7. What does `git restore hello.txt` do?
8. What does `git restore --staged hello.txt` do?
9. How can you inspect the commit history?
10. Why should you inspect `git status` and diffs before restoring a file?

## Main takeaway

**Edit → Stage → Commit**

- Edit files in the working directory.
- Use `git add` to select changes for the next commit.
- Use `git diff` and `git diff --staged` to inspect the two kinds of changes.
- Use `git commit` to save staged changes in history.
- Use `git status` frequently to understand what Git sees.
- Use `git restore` carefully because it can discard unstaged edits.

---

**Next topic:** Continue the practical exercise in the `git-practice` repository, verify what was committed, then learn how to inspect and compare versions of files over time.
