# Git Internals: The `.git` Directory, Blobs, and Trees

This section looks "under the hood" to understand *how* Git stores your data. This is not required for daily use, but it makes you a much better Git user.

## The `.git` directory: The Brains

When you run `git init`, Git creates a hidden `.git` directory. This directory is the **entire repository**. It contains all your commits, branches, tags, and configuration.

* All of your project's historical data is stored in the `.git/objects` directory.
* These "objects" are not your files directly. They are compressed, read-only data objects. There are 3 main types we care about: **Blobs**, **Trees**, and **Commits**.

## The Building Blocks: Blobs and Trees

Why does Git separate the file name from its content? This is the core concept of Git's design.

#### Blob
A **blob** (Binary Large Object) stores **only the content** of a file.
* It does *not* know the file's name, its location, or its permissions.
* Its hash (e.g., `83baae...`) is a SHA-1 hash of *only* its content.
* **This is key:** If two files in your project have the exact same content, Git stores **only one blob** and points to it from two different places. This is called **deduplication**.

#### Tree
A **tree** is Git's way of storing a **directory**.
* A tree object contains a list of pointers.
* Each pointer includes:
    1.  The **mode** (e.g., `100644` for a file).
    2.  The **type** (is it a `blob` or another `tree`?).
    3.  The **hash** of the blob or tree it's pointing to.
    4.  The **file name**.
* The **tree** object links a **file name** to a **blob** (which is just content). This is incredibly efficient.

## The Snapshot: The Commit Object

A **commit** object ties everything together into a "snapshot".
* Each commit has a unique SHA-1 hash (e.g., `3a3cbb8...`).
* This hash is generated from *all* the information in the commit:
    1.  A **tree hash**: A single pointer to the top-level tree that represents the *entire state* of your project at that moment.
    2.  A **parent hash**: The hash of the commit that came *before* this one. This is what creates the chain of history. (The first commit has no parent).
    3.  **Author/Committer info**: Your name, email, and the timestamp.
    4.  The **commit message** you wrote.

## Peeking Inside with `git cat-file`

The `git cat-file` command lets you inspect these raw Git objects. The `-p` flag stands for "pretty-print," which makes them readable.

Let's trace our first commit (`3a3cbb8`):

**Step 1: Inspect the Commit**
We ask Git to show us the commit object.

```bash
$ git cat-file -p 3a3cbb8

tree 9152961f6b8b059e761ed8a77d4c3d7e48600a7b
author Your Name <your@email.com> 1763153000 +0100
committer Your Name <your@email.com> 1763153000 +0100

A: content added
````

  * This tells us the commit points to a `tree` with the hash `9152961...`.

**Step 2: Inspect the Tree**
Now let's inspect that tree.

```bash
$ git cat-file -p 9152961

100644 blob 83baae61804e65cc73a7201a7252750c76066a30    content.txt
```

  * This tree object tells us it contains one item: a `blob` with hash `83baae6...` and the file name is `content.txt`.

**Step 3: Inspect the Blob**
Finally, let's inspect that blob.

```bash
$ git cat-file -p 83baae6

This is my first file
```

  * And there is our raw file content\! This shows the full hierarchy: **Commit -\> Tree -\> Blob**.

## The Second Commit (Snapshots & Deduplication)

Now, let's add a second file (`titles.md`) and make a new commit ("B: titles added").

Our log now looks like this:

```bash
$ git log --oneline
db540cd (HEAD -> main) B: titles added
3a3cbb8 A: content added
```

**Step 1: Inspect the *New* Commit**
Let's `cat-file` our new commit, `db540cd`.

```bash
$ git cat-file -p db540cd

tree c6a58c8f139c614485b636f652df7b589d9f8b21
parent 3a3cbb8c4cd19dca31992d67ee9f54396ca48033
author Your Name <your@email.com> 1763153435 +0100
committer Your Name <your@email.com> 1763153435 +0100

B: titles added
```

  * **Key Insight 1:** It now has a `parent` hash\! This is `3a3cbb8`, the hash of our first commit. This is the link that forms the project's history.
  * It also points to a *new* tree, `c6a58c8...`.

**Step 2: Inspect the *New* Tree**
Let's inspect this new tree.

```bash
$ git cat-file -p c6a58c8

100644 blob 83baae61804e65cc73a7201a7252750c76066a30    content.txt
100644 blob e9c5221e7883b5523363b3617e4f3a076a5966c4    titles.md
```

  * **Key Insight 2:** This tree lists *both* files.
  * **Key Insight 3 (Deduplication):** Look at the blob hash for `content.txt`. It's `83baae6...`—the **exact same hash** as before. We didn't change the file, so Git did *not* save it again. It just *re-used the pointer* to the existing blob. It only created a new blob for `titles.md`.

This is what "Git stores an entire snapshot" means. Every commit points to a tree that defines the *entire project*, but it does so efficiently by re-using any objects (blobs or trees) that haven't changed.

## A Useful Command: `git log -p`

If you want to see the *changes* in a commit without doing `cat-file`, use `git log -p`.

  * The `-p` flag (for "patch") shows the commit message *and* the diff (the exact lines that were added or removed) for each commit in your history.

<!-- end list -->

```bash
$ git log -p

commit db540cd...
Author: ...
Date:   ...

    B: titles added

diff --git a/titles.md b/titles.md
new file mode 100644
index 0000000..e9c5221
--- /dev/null
+++ b/titles.md
@@ -0,0 +1 @@
+My New Title

commit 3a3cbb8...
Author: ...
Date:   ...

    A: content added
...
```

```
```
