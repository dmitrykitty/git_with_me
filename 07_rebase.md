# Git Rebase

Rebase is one of the most powerful and often misunderstood concepts in Git. At its core, **Rebasing** is the process of moving or combining a sequence of commits to a new base commit.

While `git merge` joins two histories together, `git rebase` rewrites history to make it look like you started your work from a different point in time.

## The Concept: Moving the Base

Imagine you branch off from `main` to work on a new feature. While you are working, `main` moves forward with new updates.

**Before Rebase:**
Your branch (`new_branch`) is based on an older commit (`B`).

```text
A --- B --- E --- F    (main)
       \
        C --- D        (new_branch)
```

When you run `git rebase main` while on `new_branch`, Git takes your commits (`C` and `D`) and "replays" them on top of the latest commit of `main` (`F`).

**After Rebase:**
It looks like you started your work *after* `F` was finished.

```text
A --- B --- E --- F    (main)
                   \
                    C' --- D'   (new_branch)
```

## How it works internally

When you run:

```bash
git swotch new_branch
git rebase main
```

Git performs the following steps behind the scenes:

1.  **Identify the Base:** It looks at the latest commit on `main` (Commit `F`) and sets it as the temporary base.
2.  **Collect Commits:** It finds all unique commits on your current branch (`C` and `D`).
3.  **Replay:** It applies the changes from `C` onto `F`, creating a new commit `C'`. Then it applies `D` onto `C'`, creating `D'`.
4.  **Update Pointer:** It moves the `new_branch` label to point to the newly created `D'`.

**Note:** The original commits `C` and `D` are effectively deleted (garbage collected) and replaced by copies. This is why we say rebase **rewrites history**.

-----

## Practical Example: Rebasing a Feature Branch

Let's simulate a real scenario where we go back in time to start a branch, and then bring it up to date.

#### 1\. Create a branch from the past

First, find a commit hash from the past using `git log --oneline`. Let's say we pick `2c275ff`.

```bash
git switch -c features 2c275ff
```

Now, make some changes and commit them:

```bash
echo "New feature code" >> feature.txt
git add feature.txt
git commit -m "H: Start feature"

echo "More code" >> feature.txt
git add feature.txt
git commit -m "I: Finish feature"
```

Your history currently looks like this (diverged):

```text
              H --- I       (features)
            /
A --- B -- E -- F           (main)
      \        /
       C --- D         
```

#### 2\. Rebase onto main

We want our `features` branch to include everything currently in `main` (commits E and F) so our history is clean.

```bash
# Ensure you are on the features branch
git rebase main
```

#### 3\. The Result

Git effectively "unplugs" the `features` branch from `B` and "plugs it in" at `F`.

```text
A --- B -- E -- F (main) --- H' --- I' (features)
      \       /
       C --- D
```

Now, the `features` branch has a **linear history**. It contains all changes from `main`, followed immediately by your new work.

-----

## Philosophy: Merge vs. Rebase

There is an ongoing debate in the development world about which approach is better. It comes down to how you view your project's history.

#### The Merge Approach ("The Documentary")

  * **Philosophy:** History is a record of what actually happened. If two branches diverged and merged, the graph should show that clearly.
  * **Pros:** Preserves the exact context of commits. You can see exactly when code was written and when it was integrated.
  * **Cons:** Creates "Merge Commits" (e.g., *Merge branch 'feature' into 'main'*). In active projects, this results in a "railway track" or "spaghetti" graph that is hard to read visually.

#### The Rebase Approach ("The Storybook")

  * **Philosophy:** History should be a clean, readable story of how the project evolved. It removes the clutter of "work in progress" timelines.
  * **Pros:** Creates a perfectly straight, linear history. It is much easier to use tools like `git bisect` (bug hunting) and to read the log chronologically.
  * **Cons:** It "lies" about history (it changes the timestamp and parent of commits). It can be dangerous if used incorrectly on shared code.

-----

## The Golden Rule of Rebasing

**Never rebase a public branch (like `main` or `develop`) onto another branch.**

#### Why?

Rebasing changes the **ID (SHA hash)** of the commits.

If you rebase `main`, you are effectively deleting the old history of `main` and replacing it with a new one.

1.  Your colleagues have the "old" `main` downloaded on their computers.
2.  If you force push a rebased `main` to the server, your colleagues' history will no longer match the server's history.
3.  When they try to pull, Git will throw massive conflicts, and they will have to manually fix their repositories.

**Rule of thumb:** Only rebase branches that **only you** are working on (your local feature branches). Once a branch is shared or pushed to a team, treat its history as immutable.
