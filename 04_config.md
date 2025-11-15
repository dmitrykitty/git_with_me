# Git Config: A Deeper Dive

We configured our user name and email at the start, but `git config` is a powerful tool for managing settings at different levels.

## Config Scopes: `--global` vs. `--local`

Git settings are stored in different files, or "scopes". The two most important are:

* **`--global`**:
    * **What it is:** Settings that apply to **all** of your repositories on your user account.
    * **Where it's saved:** In a file in your home directory (`~/.gitconfig` or `~/.config/git/config`).
    * **Use case:** Your main `user.name` and `user.email`.

* **`--local`**:
    * **What it is:** Settings that apply **only** to the **current, specific repository** you are in.
    * **Where it's saved:** In a file inside your project's hidden `.git` folder (`.git/config`).
    * **Use case:** Overriding your email for a specific work project, or setting up project-specific aliases.
    * **Note:** This is the **default** scope. If you run `git config` without `--global` or `--system`, Git assumes you mean `--local`.

## Viewing Your Configuration

You have two ways to see your settings, and they do different things.

* `git config --list`:
    * This is the "Git way". It reads **all** config files (system, global, and local) and shows you the final, merged result, just as Git sees it.
    * `git config --list --global` shows *only* the global settings.
    * `git config --list --local` shows *only* the local settings.

* `cat <file>`:
    * This is the "direct way". It shows you the *raw text content* of a *single file*. It doesn't merge anything.
    * `cat ~/.gitconfig` shows the raw global file.
    * `cat .git/config` shows the raw local file.

## Adding, Getting, and Modifying Keys

You can add your own custom configuration settings.

#### Adding a Key
The format is `git config --add <scope> <section.key> <value>`.

```bash
# Adds a key named "ceo" inside a new "company" section
# This setting is saved to .git/config (local)
git config --add --local company.ceo JohnSmith
````

  * `company`: This is the **section**. It groups related keys.
  * `ceo`: This is the **key** (or name) of the specific setting.
  * `JohnSmith`: This is the **value**.

#### Getting a Key

To read that value back, just ask for the key:

```bash
$ git config company.ceo
JohnSmith
# or git config --get company.ceo
```

If you look at your `.git/config` file (`cat .git/config`), you will now see:

```ini
[core]
    ...
[company]
    ceo = JohnSmith
```

## Deleting Keys and Sections

  * **To delete a single key:**
    Use `--unset`.

    ```bash
    git config --local --unset company.ceo
    ```

  * **To delete a key with multiple values:**
    If you used `git config --add` to create a key with multiple values, `--unset` only removes one. To remove all of them:

    ```bash
    git config --local --unset-all some.multivalue-key
    ```

  * **To delete an entire section:**
    This removes the section and *all* keys inside it.

    ```bash
    git config --local --remove-section company
    ```
-----

# The Hierarchy: How Settings are Overridden

This is the most important concept. Git reads settings from 4 levels in a specific order. The **most specific level always wins**.

Here is the hierarchy from *broadest* (lowest priority) to *most specific* (highest priority):

1.  **`system`**

      * **Scope:** Applies to *every user* on the *entire machine*.
      * **File:** `/etc/gitconfig`

2.  **`global`**

      * **Scope:** Applies to *all projects* for the *current user*.
      * **File:** `~/.gitconfig` (in your home directory)

3.  **`local`**

      * **Scope:** Applies *only* to the *current repository*.
      * **File:** `.git/config` (inside your project)

4.  **`worktree`** (Advanced)

      * **Scope:** Applies only to a single worktree if you use the `git worktree` feature.
      * **File:** `.git/config.worktree`

#### Overriding Example:

Imagine this setup:

  * Your **global** file (`~/.gitconfig`) has:
    `user.email = "my-personal-email@gmail.com"`
  * Your **local** file (`.git/config`) in your work project has:
    `user.email = "my-work-email@company.com"`

When you are inside that project and run `git config user.email`, the output will be:

```
my-work-email@company.com
```

because the **local** setting overrides the **global** one.
