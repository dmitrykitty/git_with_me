# Installation

Before you can configure Git, you need to install it on your system. The method varies depending on your operating system.

### On Linux (Debian/Ubuntu-based)

```bash
# First, update your package list
sudo apt update
# Then, install Git
sudo apt install git
```

### On macOS
The easiest way to install Git on a Mac is by using the [Homebrew](https://brew.sh/) package manager.
```bash
# Install Git using Homebrew
brew install git
```
*Note: If you don't have Homebrew, you can also get Git by installing the Xcode Command Line Tools from Apple.*

### On Windows

For Windows, the most straightforward method is to download the official installer from the Git website. This package includes Git Bash, a terminal that allows you to run Git commands in a Unix-like environment.
- Download the installer from [git-scm.com](https://git-scm.com/download/)

---

# Set Up: Configuring Your Identity

This is the first step you should take after installing Git. Your **name and email** are attached to every single commit you make, so this identifies you as the author.

### Checking Your Current Configuration

Before setting anything, you can check if a name and email are already configured on your machine.

```bash
# Check the currently set user name
git config user.name

# Check the currently set user email
git config user.email

# Alternative: List all settings and filter for "name"
# git config --list | grep name
```

### Setting Your Global Configuration
These commands set your identity for all Git repositories on your computer (that's what the `--global` flag does).

*Note: You should use the same email address that you use for your GitHub, GitLab, or other Git hosting account.*

```bash
# Sets your user name for all repositories
git config set --global user.name "github_username_here"

# Sets your email for all repositories
git config set --global user.email "email@example.com"
```

### Viewing the Config File

Git stores these global settings in a file named `.gitconfig` located in your user's home directory (`~/`). You can view the raw contents of this file to confirm your settings were saved.
```bash
# 'cat' is a command to display the contents of a file
cat ~/.gitconfig
```
