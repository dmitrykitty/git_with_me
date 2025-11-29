# GitHub: The Social Network for Code

We have learned how to manage code locally and even simulate a remote on our own computer. Now, let's move to the real world.

## Git vs. GitHub (They are not the same!)

This is the most common confusion for beginners.

* **Git** is the tool (the version control system). It runs locally on your machine. You don't need internet to use it.
* **GitHub** is a service (a website) that hosts Git repositories on the internet.

---

## Setting Up Your First Cloud Repo

1.  **Create an Account:** Go to [github.com](https://github.com/) and sign up.
2.  **Create a Repo:**
    * Click the **+** icon in the top-right corner.
    * Select **New repository**.
    * Name it `testing_repo`.
    * Leave it **Public**.
    * *Do not* initialize it with a README (we want it empty so we can push our existing code).
    * Click **Create repository**.

### A Note on Security: HTTPS vs. SSH
When connecting to GitHub, you need to authenticate so they know it's you. There are two ways:

1.  **HTTPS:** Uses your username and a Personal Access Token. Easier to start, but you might have to type credentials often.
2.  **SSH (Secure Shell):** You create a "Key Pair" on your computer (a public key and a private key). You give the public key to GitHub.
    * **Why use it?** It is generally more secure and convenient. Once set up, you never have to type a password to push code.

---

## Connecting Local to Remote

Now that you have an empty repo on GitHub, let's connect our local project to it.

```bash
# Remove the old "fake" origin if you still have it
git remote remove origin

# Add the new GitHub URL
git remote add origin [https://github.com/nikitin-prog/testing_repo.git](https://github.com/nikitin-prog/testing_repo.git)
````

### Verifying the Connection

```bash
$ git remote -v
origin  [https://github.com/nikitin-prog/testing_repo.git](https://github.com/nikitin-prog/testing_repo.git) (fetch)
origin  [https://github.com/nikitin-prog/testing_repo.git](https://github.com/nikitin-prog/testing_repo.git) (push)
```

> **What does `-v` mean?**
> It stands for **verbose**. Standard commands often hide details to keep things clean. Adding `-v` tells the command to "tell me everything you know." Here, it shows both the URL for downloading (fetch) and uploading (push).

You can also use this command to check if the server is reachable:

```bash
git ls-remote
# Output: Lists the current refs (commits) on the server
```

-----

## Pushing Code (`git push`)

We have the connection, but GitHub is still empty. We need to upload our commits.

```bash
git push origin main
```

**What does this do?**
It takes your local `main` branch and uploads all the commits, trees, and blobs to the `origin` remote, updating the `main` branch there to match yours.

### Advanced Pushing

  * **Setting Upstream (`-u`):**
    If you run `git push -u origin main`, Git remembers that your local `main` is linked to `origin/main`. In the future, you can just type `git push` without arguments.

  * **Pushing with Different Names:**
    You can push a local branch to a remote branch with a different name.
    `git push origin <local_branch_name>:<remote_branch_name>`

  * **Deleting a Remote Branch:**
    This is a weird syntax trick. You "push nothing" into the remote branch.
    `git push origin :<remote_branch_name>`
    *(Modern Git also allows `git push origin --delete <branch_name>` which is easier to remember).*

> **Important Note on Emails:**
> GitHub uses the email address in your commits to link them to your user profile. If your `git config user.email` does not match the email you used to sign up for GitHub, your commits will appear, but they won't have your avatar or show up in your contribution stats.

-----

## Pulling Changes (`git pull`)

Let's simulate a teammate making a change. Go to your repo on the GitHub website, open a file (e.g., `classics.csv`), edit it, and commit the change directly in the browser.

Now your local repo is **out of date**.

```bash
git pull origin main
```

As we learned, `git pull` is actually `git fetch` + `git merge`.

### Configuration: Rebase vs. Merge

Git might ask you how you want to handle pulls. A common safe default is:

```bash
git config pull.rebase false
```

**What does this do?**
It tells Git: "When I pull new changes, if my history has diverged from the server, please create a **Merge Commit** to join them together." (The alternative is `true`, which rebases your local changes on top of the server's changes).

After pulling the changes from GitHub, if you check your log:

```bash
$ git log -2 --oneline
9a65b6d (HEAD -> main) K: Merge commit (Merge branch 'main' of github.com...)
9756832 (origin/main) J: Update classics.csv
```

You can see the commit made on GitHub (`J`) and the merge commit (`K`) that brought it into your local history.

-----

## The Pull Request (PR) Workflow

In a professional team, you rarely push directly to `main`. You use **Pull Requests**.

A Pull Request is not a Git command; it is a GitHub feature. It is a request to merge your branch into the main codebase, allowing your team to review and approve the code first.

### Step 1: Create a Branch & Changes

```bash
# Create and switch to a new branch
git switch -c add_classics

# Make changes...
echo "The Matrix,1999" >> classics.csv

# Commit
git add classics.csv
git commit -m "Add The Matrix"
```

### Step 2: Push the Branch

We push this specific branch to GitHub.

```bash
git push -u origin add_classics
```

### Step 3: Open the PR

1.  Go to your GitHub repository page.
2.  You will often see a yellow banner: "add\_classics had recent pushes. **Compare & pull request**". Click it.
3.  Write a title and description explaining *why* you made these changes.
4.  Click **Create pull request**.

### Step 4: Review and Merge

Now, your teammates can see the code differences (diffs), leave comments, and approve the changes.

Once approved, you click the green **Merge pull request** button on GitHub. GitHub performs the merge on the server, updating the `main` branch there.

### Step 5: Sync Local

Back on your computer, `main` is still old.

```bash
git switch main
git pull origin main
```

Now everyone is in sync\!
