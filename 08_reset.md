# Git Reset: Undoing Changes

Now that we know how to add commits and merge branches, we need to learn how to get our files back if we mistakenly rewrite them.

## The Setup: Making a Mistake

Let's intentionally simulate a disaster where we overwrite a good file with bad data.

```bash
echo "# Mistake" > title.md
git add title.md
git commit -m "Mistake made"
```

At this point, our `title.md` file is ruined, and worse, we have saved that ruin into the history.

The `git reset` command is the primary tool to undo recent commits. It can modify the **Commit History**, the **Staging Area (Index)**, and the **Working Directory**, depending on the "mode" you use.

-----

### 1\. The Soft Reset (`--soft`)

**The "Undo Commit" Button.**

This moves the branch pointer back, but it **preserves your work**. It leaves your changes in the "Staging Area" (Index), so they are ready to be committed again (perhaps with a fixed file or a different message).

Let's undo that mistake commit:

```bash
git reset --soft HEAD~1
```

>Note: `HEAD~` notation works reliably on linear history. If you have complex merges, be careful, as discussed in the previous chapter.*

Now, if we check the status:

```bash
$ git status
On branch features
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   title.md
```

We can verify exactly what the changes are (the mistake is still there in the file, but it is no longer "committed"):

```bash
git diff --staged
# Or
git log -p --oneline
```

**Output:**

```diff
-# Titles
-
-- A River Runs Through It
-- Fight Club
-- Dune
+# Mistake
```

**Summary:** With `git reset --soft`, you go back in time in the history, but you keep the current state of your files staged.

-----

### 2\. The Hard Reset (`--hard`)

**The "Delete Forever" Button.**

This moves the branch pointer back **and** makes your Working Directory match that older commit perfectly. Any work done in the commits you jump over is destroyed.

Let's pretend we made the "Mistake" commit again. This time, we want to completely destroy it and revert the file to how it looked before.

```bash
git reset --hard HEAD~1
```

**What happens:**

1.  The commit "Mistake made" is removed from the history.
2.  The file `title.md` is physically rewritten on your disk to match the previous commit.
3.  The text `"# Mistake"` is gone. The original list of titles is back.

-----

## FAQ: Reset Nuances

#### Does `git reset --hard` delete untracked files?

**Generally, No.**

If you create a new file (e.g., `secret_notes.txt`) but **never** add it to git (it is "Untracked"), `git reset --hard` will leave it alone. Git only modifies files it is tracking.

*However*, if the commit you are resetting *to* contains a file named `secret_notes.txt`, Git might overwrite your untracked file to match the commit. But typically, untracked files are safe from a reset.

#### Is it possible to recover files after `git reset --hard`?

**Yes, but with a catch.**

If you committed the files (even if you just reset them away), Git still has them in its database for a short time. You can find them using the **Reflog**.

1.  **Check the Reflog:**

    ```bash
    git reflog
    ```

    *This shows a log of everywhere HEAD has been, even if you reset.*

    ```text
    a1b2c3d HEAD@{0}: reset: moving to HEAD~1
    99z88y7 HEAD@{1}: commit: Mistake made
    ```

2.  **Recover the lost commit:**
    Identify the hash of the commit you just deleted (`99z88y7` in the example above) and reset back to it:

    ```bash
    git reset --hard 99z88y7
    ```

>**Warning:** If you had changes in your working directory that you **never added or committed**, and you ran `git reset --hard`, those specific changes are lost forever. Git cannot recover what it never saw.
