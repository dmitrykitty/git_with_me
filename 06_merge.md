
# Merging: Bringing History Together

We have learned how to create branches to work in isolation. Now, we need to learn how to combine that work back into the `main` branch. This process is called **merging**.

## The Setup: Diverged History

Imagine a common scenario. You started a new feature, but while you were working on it, something changed on the main branch (maybe a hotfix or a teammate's work).

Let's visualize the structure you described:
1.  You were on `main` at **Commit B**.
2.  You created `my_new_branch` and added **Commit C** and **Commit D**.
3.  You switched back to `main` and made a new **Commit E**.

Your history now looks like this:

```text
A - B - E      (main)
     \
      C - D    (my_new_branch)
````

If you run `git log --oneline --all --graph`, you will see exactly this structure:

```bash
* 3ece5b1 (HEAD -> main) E: content changed
| * 98f805c (my_new_branch) D: json added
| * 2c275ff C: classics added
|/
* db540de B: titles added
* 3a3cbb8 A: content created and added
```

## How a Merge Works (The Theory)

When you want to merge `my_new_branch` into `main`, Git performs a specific calculation.

 1.  **Find the Merge Base:** Git looks for the "Best Common Ancestor". In our case, this is **Commit B**. It is the last point where the two branches were together.
 2.  **Create a Merge Commit:** Git takes the changes from your branch (C+D) and combines them with the changes on main (E). It creates a special **new commit** (Commit F) to finalize this combination.

## Performing the Merge

Make sure you are on the branch you want to merge *into* (usually `main`).

```bash
# 1. Switch to main
git switch main

# 2. Merge the feature branch
git merge my_new_branch
```

#### The Editor (Vim)

When you run this, Git will likely open a default text editor (often **Vim**) to ask for a commit message. It might look scary if you haven't used it before.

**How to handle Vim:**

1.  **Read mode:** You start in read mode. You can't type yet.
2.  **Insert mode:** Press **`i`** on your keyboard. Now you can type/edit the message (e.g., "F: Merge branch 'my\_new\_branch'").
3.  **Save & Exit:**
      * Press **`Esc`** (to exit insert mode).
      * Type **`:wq`** (colon, w, q). This stands for "Write" and "Quit".
      * Press **Enter**.

## The Result: A Merge Commit

Now your history looks like this. The two lines of history have joined back together.

```text
A - B - E - F      (main)
     \     /
      C - D        (my_new_branch)
```

Let's look at the log command:

```bash
$ git log --oneline --all --graph --parents

* 9624dfe 3ece5b1 98f805c (HEAD -> main) F: Merge branch 'my_new_branch'
|\
| * 98f805c 2c275ff (my_new_branch) D: json added
| * 2c275ff db540de C: classics added
* | 3ece5b1 db540de E: content changed
|/
* db540de 3a3cbb8 B: titles added
* 3a3cbb8 A: content created and added
```

**Understanding the Parameters:**

  * `--oneline`: Short view (hash + message).
  * `--all`: Shows all branches, not just the current one.
  * `--graph`: Draws the visual lines (`|`, `\`, `*`) on the left.
  * `--parents`: **Crucial part.** Look at the first line (Commit F). It shows `9624dfe` (the commit hash) followed by **two** parent hashes: `3ece5b1` (Commit E) and `98f805c` (Commit D).
      * **Normal commits** have 1 parent.
      * **Merge commits** have 2 (or more) parents. This is how Git remembers that this commit joins two histories.

## Cleaning Up

Now that the work from `my_new_branch` is safely inside `main`, we don't need the pointer anymore.

```bash
git branch -d my_new_branch
```

> *Note: This only deletes the branch label (pointer). The commits C and D are safe because they are now part of the `main` history.*

-----

### 6\. Fast-Forward Merge (The "Easy" Case)

There is a simpler type of merge called a **Fast-Forward**.

This happens if `main` has **not changed** while you were working on your branch.

**Scenario:**

```text
A - B (main)
       \
        C - D (feature)
```

Here, there is no divergence. The "Merge Base" is Commit B, which is exactly where `main` is.

**Action:**
When you run `git merge feature`, Git doesn't need to create a new commit. It simply **picks up the `main` label and moves it forward** to Commit D.

**Result:**

```text
A - B - C - D (main, feature)
```

It's a linear history. No "merge commit" is created.
