
# The Basic Workflow: Init, Add, Commit & Status

This section covers the most fundamental lifecycle of a Git repository: creating a repo, making changes, saving them to history, and checking your status.

### Starting a Repository (`git init`)

Before you can add or commit files, you need a repository. This command **initializes** a brand new, empty Git repository in your current folder. It creates a hidden `.git` directory that will track all your changes.

```bash
# Create a new folder for your project
mkdir my-project
cd my-project

# Turn this folder into a Git repository
git init
````

After this, you can start creating files.

### The Three-Stage Workflow (The Core Concept)

Before using the commands, it's crucial to understand the three "areas" Git uses to manage your files:

1.  **Working Directory:** The actual folder and files on your computer that you can see and edit.
2.  **Staging Area (or "Index"):** A "waiting room" or "drafting" area. This is where you place changes you *want* to include in your next "snapshot" (commit).
3.  **Repository (.git directory):** The permanent history. This is where Git stores all your "snapshots" (commits).

The commands `git add` and `git commit` move your changes between these areas.

### Step-by-Step: Your First Commit

Let's walk through the full cycle *after* you have run `git init`.

#### Step 1: Create a File

First, create a new file in your repository folder.

```bash
# You can use a text editor like nano
nano content.txt
# (Inside nano, type "Hello World", then Ctrl+O to save, Ctrl+X to exit)

# Or, for a quick example, use 'echo'
echo "This is my first file" > content.txt
```

#### Step 2: Check Status (Untracked Files)

Now, ask Git what it thinks about this new file.

```bash
$ git status

#On branch main
#No commits yet

#Untracked files:
#  (use "git add <file>..." to include in what will be committed)
#        content.txt

#nothing added to commit but untracked files present (use "git add" to track)
```

> **What does "Untracked files" mean?**
> This means Git **sees** a new file in your Working Directory, but it is **not tracking** its history. It's not in the Staging Area and it's not in your Repository history. Git is ignoring it until you explicitly tell it to track the file.

#### Step 3: Add to Staging Area (`git add`)

Now we tell Git, "I want to include this file in my next snapshot." We use `git add` to move the file from the Working Directory to the Staging Area.

```bash
# Stage a specific file
git add content.txt
```

> **What's the difference?**
>
>   * `git add <path>` (e.g., `git add content.txt`): Stages **only** the specified file or directory. This is precise and safe.
>   * `git add .`: Stages **all** new and modified files in your current directory and all subdirectories. This is a common and powerful shortcut, but use it with care to avoid adding files you don't want (like password files or build artifacts).

#### Step 4: Check Status (Changes to be Committed)

Let's check `git status` again. Notice the message is different.

```bash
$ git status

#On branch main
#No commits yet

#Changes to be committed:
#  (use "git rm --cached <file>..." to unstage)
#        new file:   content.txt
```

> **What does "Changes to be committed" mean?**
> This confirms that `content.txt` is now in the **Staging Area**. It is "staged" and ready to be permanently saved. The `git rm --cached` hint tells you how to "unstage" it (move it *out* of the staging area) if you made a mistake.

#### Step 5: Create a Commit (`git commit`)

A **commit** is a **snapshot** of your Staging Area at a specific point in time. It's how Git saves history. Each commit is a permanent record that builds on the last one, creating a chain of changes.

We use `git commit` and the `-m` flag to provide a short, descriptive message.

```bash
git commit -m "A: content added"
```

>   * **What is a commit?** It's a permanent save point in your project's history.
>   * **What does `-m` mean?** It stands for "message". Every commit **must** have a message. This message explains *why* you made this change. Good, clear messages are crucial.

#### Step 6: Check Status (Working Tree Clean)

Finally, let's check our status one last time.

```bash
$ git status

#On branch main
#nothing to commit, working tree clean
```

> **What does "working tree clean" mean?**
> This is the perfect state. It means your **Working Directory**, **Staging Area**, and the **Repository's last commit** are all in sync. You have no pending or unstaged changes.

-----

## Viewing Your History (`git log`)

Now that you have a commit, you can view your project's history.

### Standard Log

The default `git log` command is very detailed.

```bash
$ git log

#commit 3a3cbb8b64f3d17983693e50b731003b5736184a (HEAD -> main)
#Author: Your Name <your@email.com>
#Date:   Fri Nov 14 17:40:00 2025 +0100

#    A: content added
```

### One-Line Log

A much more useful view is `git log --oneline`.

```bash
$ git log --oneline
#3a3cbb8 (HEAD -> main) A: content added
```

> **What does this mean?**
>
>   * `3a3cbb8`: This is the **commit hash**, a unique 7-character ID for that snapshot.
>   * `(HEAD -> main)`: `HEAD` is a pointer showing what commit you are *currently* on. `main` is the name of the branch you are on.
>   * `A: content added`: This is the commit message you wrote.

### A More Powerful Log Command

Here is a very useful log command and a breakdown of its parameters:

```bash
git --no-pager log -n 10 --oneline --parents --graph
```

  * `git --no-pager log`: The `--no-pager` part tells Git to print the log directly to your terminal, instead of opening an interactive window (called a "pager").
  * `-n 10`: (short for `--max-count=10`) Shows only the last **10** commits.
  * `--oneline`: Shows the compact, one-line view (hash + message).
  * `--parents`: Shows the parent commit(s) for each commit.
  * `--graph`: Draws a small
    ASCII art graph showing how the branches connect (very useful in complex projects).

-----

## Summary: The 3 Key Commands

This entire workflow boils down to three essential commands you will use constantly (plus `git init` once at the start):

1.  `git status`
      * **Question it answers:** "Where am I? What's the state of my project?"
2.  `git add [file]` or `git add .`
      * **Action it performs:** "Prepare this change to be saved." (Moves changes from Working Directory to Staging Area).
3.  `git commit -m "Your message"`
      * **Action it performs:** "Save this snapshot permanently." (Moves changes from Staging Area to the Repository History).
