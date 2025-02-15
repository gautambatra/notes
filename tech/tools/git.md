## 1. Create a Repository
```
# By default creates "master" branch, more common to use "main"
# instead. Github also has main as default branch.
git init [-b <branch>]
```

## 2. Add/remove files/directories 
```
git add <files>

# Add all modified/deleted files (doesn't add new files)
git add -u

# Unstaging all files from stage
git reset

# Remove files from working tree at index
git rm [-r] [-f] <files> 

# Remove only from repo and not from disk
git rm [-r] --cached <files>

# truncate reflog (cannot recover deleted files)
git gc --prune=now --aggressive
git repack
```

## 3. See status
```
git status | less
git status -uno # Don't show untracked files

# list files in repo
git ls-tree --full-tree -r --name-only HEAD

# list directories in repo
git ls-files | xargs -n 1 dirname | uniq

# Show number of objects in repo and size
git count-objects -v -H
```

## 4. Commit
```
# Commit all staged files
git commit -m "message"

# automatically stage all modified files (new files not affected)
git commit -a -m "message"

# Commit specific files
```

## 5. Add remote URL to git repo
```
git remote add origin <repo url>

git remote set-url origin git@github.com:username/repo.git

```

## 6. Pushing initial changes to remote repo
```
git push origin <branch>


# Not sure why I had to do this for initial push to work:
git config pull.rebase true
git pull origin main
git push origin main
```

# Using a bare repository to manage dotfiles
```bash
# First time git setup:
git config --global user.email "someone@something.com"
git config --global user.name "Gautam Batra"
git config --global init.defaultBranch main

# Set these variables/have them in bashrc
export MY_HOME_DIR=$HOME
export MY_DOTFILES=$MY_HOME_DIR/dotfiles

# initialise the bare git repo
mkdir $MY_DOTFILES
git init --bare $MY_DOTFILES

# Create aliases for managing config using git
alias confgit='git --git-dir=$MY_DOTFILES --work-tree=$MY_HOME_DIR'

# Don't show untracked files for confgit
confgit config status.showUntrackedFiles no

# Adding files
confgit add <files>

# Committing
confgit commit -m "message"

# Remote
confgit remote add origin https://github.com/gautambatra/dotfiles.git
confgit remote set-url origin git@github.com:gautambatra/dotfiles.git

```
