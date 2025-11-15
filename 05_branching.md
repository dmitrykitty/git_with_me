# Branching: Working in Parallel

## What is a Branch? (The Core Concept)

A branch is **not** a copy of your project. Think of a branch as a **lightweight, movable pointer** to a specific commit.

When you start, your history is linear. The default branch (e.g., `main`) is just a pointer to your last commit.

```

(Commit A) \<-- (Commit B) \<-- (HEAD, main)

```

When you create a new branch, you are just creating a **second pointer** that points to the *same commit* you are currently on.

```

                               <-- (HEAD, new-feature)
                             /
(Commit A) \<-- (Commit B) \<-- (main)

```
* `HEAD` is another pointer that tells Git which branch you are *currently working on*.

This is why creating a branch is instant. You're not copying files; you're just creating a 40-character text file.

## Checking Your Current Branch

To see all your local branches and find out which one you're on, use `git branch`.

```bash
$ git branch
* main
  staging
  development
````

  * The asterisk (`*`) shows you which branch is currently active (i.e., where `HEAD` is pointing).

## Renaming `master` to `main` (Best Practice)

The historical default branch name was `master`. The modern standard is `main`. It's good practice to use `main`.

#### Step 1: Set Your Default for Future Repos

You can tell Git to always name the first branch `main` in any new repository you create with `git init`.

```bash
# Check what your current default is
git config --get --global init.defaultBranch

# If it's not 'main', set it globally
git config --global init.defaultBranch main
```

> **What does "default branch" mean?**
> It's simply the name that Git gives to the very first branch it creates when you run `git init` in a new, empty repository.

#### Step 2: Rename the Branch in Your *Current* Repo

If you have an existing repo that uses `master`, you can rename it.

```bash
# Renames the local branch. Make sure you are not on it first.
git branch -m master main
```

> **What does `-m` mean?**
> The `-m` flag stands for **`--move`**. You are "moving" (renaming) the branch pointer from `master` to `main`.

## Creating and Switching Branches

There are two main ways to create a new branch.

#### Method 1: Create, then Switch

You can create a new branch with `git branch`, but this **does not** switch to it.

```bash
# Creates the new branch pointer
git branch new-feature

$ git branch
* main
  new-feature

# You are still on 'main'. You must now switch to it.
git switch new-feature
```

#### Method 2: Create and Switch (Recommended)

The modern and easier way is to do both at once using `git switch -c`.

```bash
# The -c flag stands for "create"
git switch -c new-feature
```

  * This single command creates the `new-feature` branch and immediately moves your `HEAD` pointer to it.

> **What about `git checkout -b`?**
> This is the older, classic command that does the **exact same thing** as `git switch -c`.
>
> ```bash
> # This is the old syntax. -b stands for "branch".
> git checkout -b new-feature
> ```
>
> Both are fine, but `git switch` is the new, clearer command.

## How Commits Work on a Branch

Let's say you are on your `new-feature` branch and you make a new commit ("C: added new file").

1.  Git creates the new commit object (Commit C).
2.  Commit C's parent is set to Commit B (where you were).
3.  Git **moves the `new-feature` pointer** forward to Commit C.
4.  The `main` pointer **stays exactly where it was**.

Your history has now **diverged**:

```
                             (Commit C) <-- (HEAD, new-feature)
                           /
(Commit A) <-- (Commit B) <-- (main)
```

This is what you saw:

  * `git log` (while on `new-feature`) shows: C, B, A.
  * `git log main` (which shows the log for *only* the `main` branch) shows: B, A.

## Viewing Branch History (Decorations)

How does `git log` show you which branches are on which commits? It uses "decorations".

```bash
# This is the most useful log command to see everything
git log --oneline --graph --decorate
```

  * `--oneline`: Shows the compact, one-line view.
  * `--graph`: Draws the ASCII art graph showing the branching.
  * `--decorate`: (Default in modern Git) Shows the branch names in parentheses, like `(HEAD -> new-feature)`, `(main)`.

You can control this behavior:

  * `--decorate=full`: The default, shows full names like `(HEAD -> refs/heads/new-feature)`.
  * `--decorate=short`: A cleaner view, like `(HEAD -> new-feature)`.
  * `--decorate=no`: Hides all branch names from the log.

## Git Internals: Where are Branches Stored?

Branch information is stored in the `.git` folder.

  * **Location:** `.git/refs/heads/`
  * **What is stored there?** This directory contains **simple text files**, one for each branch.
  * If you run `cat .git/refs/heads/main`, you will see a single line: the 40-character SHA-1 hash of the commit that `main` currently points to.
  * If you run `cat .git/refs/heads/new-feature`, you'll see the hash for "Commit C".

When you "commit to a branch," all Git does is update the SHA-1 hash inside that tiny text file to point to your new commit. This is why branching and committing are lightning fast.
