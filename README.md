# Git Cheatsheet - A Quick Reference

Welcome to my repository! This is a collection of the most important and frequently used Git commands that I've compiled for myself and others.

Everything here is based on the excellent [Learn Git course from boot.dev](https://www.boot.dev/courses/learn-git).

---

# Topics

### [**1.1 Installation & Setup**](./01_setup.md)
  * Installing Git on Mac/Windows/Linux;
  * Configuring user name and email;

### [**1.2 Basics of Git**](./02_basics.md)
  * Initializing a repo with `git init`;
  * The 3-stage workflow (Working, Staging, Repo);
  * Checking status with `git status`;
  * Staging files with `git add`;
  * Committing changes with `git commit`;
  * Viewing history with `git log`;

### [**1.3 Git Internals**](./03_internals.md)
  * How Git stores data: **blobs** (content) & **trees** (filenames);
  * What a **commit** object is (a snapshot + parent link);
  * Peeking inside the `.git` directory;
  * Using `git cat-file` to inspect raw objects;
  * How Git's snapshot model saves space (deduplication);

### [**1.4 Configuration**](./04_config.md)
  * Config scopes: `--global` vs `--local`;
  * Viewing settings with `git config --list`;
  * Adding, getting, and deleting configuration keys;
  * The Hierarchy: How local settings override global ones;

### [**1.5 Branching**](./05_branching.md)
  * What is a branch? (A lightweight movable pointer);
  * Creating and switching branches with `git switch`;
  * Renaming `master` to `main`;
  * Understanding `HEAD` and detached states;
  * Viewing decorations in `git log`;

### [**1.6 Merging**](./06_merge.md)
  * Combining diverged history with `git merge`;
  * Understanding Merge Commits (two parents);
  * Fast-Forward merges (linear history);
  * Handling the default editor (Vim) during merges;

### [**1.7 Rebasing**](./07_rebase.md)
  * Rewriting history to move a branch base;
  * How `git rebase` works internally (replay commits);
  * The philosophy: Merge ("The Documentary") vs. Rebase ("The Storybook");
  * The Golden Rule: Never rebase public branches;

### [**1.8 Reset & Recovery**](./08_reset.md)
  * Undoing changes with `git reset`;
  * `--soft`: Undo the commit, keep the work staged;
  * `--hard`: Delete the commit and the work (destructive);
  * Recovering "lost" commits using `git reflog`;

### [**1.9 Remote Repositories**](./09_remote.md)
  * Understanding remotes (just another folder);
  * `origin` vs `upstream`;
  * Adding a remote with `git remote add`;
  * The difference between `git fetch` (download) and `git merge` (update);

### [**1.10 GitHub & Collaboration**](./10_github.md)
  * Git vs. GitHub (Tool vs. Service);
  * HTTPS vs. SSH keys;
  * Pushing code with `git push` and setting upstream (`-u`);
  * Pulling strategies (`pull.rebase false`);
  * The Professional Workflow: Branch -> Push -> **Pull Request** -> Merge;


