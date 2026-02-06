# Git Troubleshooting Guide

A comprehensive guide for resolving common Git authentication and merge conflict issues in DevOps workflows.

---

## Table of Contents

1. [Authentication Issues](#authentication-issues)
   - [HTTPS Authentication Failures](#https-authentication-failures)
   - [SSH Key Issues](#ssh-key-issues)
   - [Personal Access Token (PAT) Issues](#personal-access-token-pat-issues)
   - [GitHub CLI Authentication](#github-cli-authentication)
2. [Merge Conflicts](#merge-conflicts)
   - [Understanding Conflicts](#understanding-conflicts)
   - [Resolving File Conflicts](#resolving-file-conflicts)
   - [Stash Conflicts](#stash-conflicts)
   - [Rebase Conflicts](#rebase-conflicts)
3. [Common Scenarios & Solutions](#common-scenarios--solutions)
4. [Best Practices](#best-practices)

---

## Authentication Issues

### HTTPS Authentication Failures

#### Error: `remote: Invalid username or token. Password authentication is not supported`

**Cause:** GitHub deprecated password authentication for Git operations. You must use SSH keys or Personal Access Tokens.

**Solutions:**

**Option 1: Switch to SSH (Recommended)**

```bash
# 1. Check if you have an SSH key
ls -la ~/.ssh/

# 2. If no key exists, generate one
ssh-keygen -t ed25519 -C "your-email@example.com"

# 3. Add your SSH key to the ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 4. Copy the public key
cat ~/.ssh/id_ed25519.pub

# 5. Add the public key to GitHub:
#    - Go to GitHub → Settings → SSH and GPG keys → New SSH key
#    - Paste your public key

# 6. Test SSH connection
ssh -T git@github.com
# Expected output: "Hi username! You've successfully authenticated..."

# 7. Switch remote from HTTPS to SSH
git remote set-url origin git@github.com:USERNAME/REPOSITORY.git

# 8. Verify the change
git remote -v

# 9. Push your changes
git push origin main
```

**Option 2: Use Personal Access Token (PAT)**

```bash
# 1. Generate a PAT on GitHub:
#    - Go to GitHub → Settings → Developer Settings → Personal Access Tokens
#    - Generate a new token with 'repo' scope

# 2. Store PAT in Git credential helper
git config --global credential.helper store

# 3. On next push, enter username and PAT as password
git push origin main
# Username: your-github-username
# Password: paste-your-PAT-here
```

---

### SSH Key Issues

#### Error: `Permission denied (publickey)`

**Causes:**
- SSH key not added to GitHub
- SSH agent not running
- Wrong key being used

**Solution:**

```bash
# 1. Verify SSH agent is running
eval "$(ssh-agent -s)"

# 2. List loaded keys
ssh-add -l

# 3. If empty, add your key
ssh-add ~/.ssh/id_ed25519

# 4. Test authentication
ssh -T git@github.com

# 5. Debug connection issues
ssh -vT git@github.com
```

#### Error: `Could not open connection to authentication agent`

**Solution:**

```bash
# Start the SSH agent
eval "$(ssh-agent -s)"

# Add your key
ssh-add ~/.ssh/id_ed25519
```

#### Wrong key being used (multiple SSH keys)

```bash
# Create/edit SSH config file
cat > ~/.ssh/config << 'EOF'
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519
  IdentitiesOnly yes
EOF

chmod 600 ~/.ssh/config
```

---

### Personal Access Token (PAT) Issues

#### Error: `fatal: Authentication failed` (with PAT)

**Causes:**
- Token expired
- Token lacks required scopes
- Token revoked

**Solution:**

```bash
# 1. Clear cached credentials
git config --global --unset credential.helper
git credential-cache exit 2>/dev/null || true

# 2. Remove stored credentials (if using 'store')
rm ~/.git-credentials 2>/dev/null || true

# 3. Generate new PAT with correct scopes:
#    - Go to GitHub → Settings → Developer Settings → Personal Access Tokens
#    - Required scopes: repo, workflow (for GitHub Actions)

# 4. Set up credential helper again
git config --global credential.helper store

# 5. Push and enter new credentials
git push origin main
```

---

### GitHub CLI Authentication

#### Error: `The token in hosts.yml is invalid`

**Cause:** GitHub CLI token expired or was revoked.

**Solution:**

```bash
# 1. Check current auth status
gh auth status

# 2. Re-authenticate
gh auth login -h github.com

# 3. Choose authentication method:
#    - SSH (recommended)
#    - HTTPS with PAT

# 4. If using GitHub CLI for Git credential management, refresh tokens
gh auth setup-git
```

---

## Merge Conflicts

### Understanding Conflicts

Conflicts occur when:
- Two branches modify the same lines in a file
- One branch deletes a file that another modifies
- Concurrent changes to the same section of code

**Conflict markers:**

```
<<<<<<< HEAD
Your local changes
=======
Incoming changes from the merge
>>>>>>> branch-name
```

---

### Resolving File Conflicts

#### Step-by-Step Resolution

```bash
# 1. Attempt the merge
git merge feature-branch

# 2. If conflicts occur, check status
git status

# 3. View conflicted files
git diff --name-only --diff-filter=U

# 4. Open conflicted file and resolve manually
#    Remove conflict markers and keep desired code

# 5. After resolving, stage the file
git add <resolved-file>

# 6. Complete the merge
git commit -m "Resolve merge conflicts"

# 7. If you want to abort the merge
git merge --abort
```

#### Using VS Code to Resolve Conflicts

```bash
# Open VS Code merge editor
code <conflicted-file>

# VS Code provides options:
# - Accept Current Change (yours)
# - Accept Incoming Change (theirs)
# - Accept Both Changes
# - Compare Changes
```

#### Using Git's Built-in Merge Tool

```bash
# Configure merge tool
git config --global merge.tool vimdiff

# Launch merge tool
git mergetool
```

---

### Stash Conflicts

#### Error: `error: Your local changes would be overwritten`

```bash
# 1. Stash your changes
git stash

# 2. Pull or switch branches
git pull origin main

# 3. Apply stash
git stash pop

# 4. If conflicts occur during stash pop
# Resolve conflicts, then:
git stash drop  # Remove the stash entry

# 5. Alternatively, keep stash and create new branch
git stash branch new-feature-branch
```

---

### Rebase Conflicts

#### Resolving Conflicts During Rebase

```bash
# 1. Start rebase
git rebase main

# 2. When conflict occurs
git status  # Shows conflicted files

# 3. Resolve conflicts in each file

# 4. Stage resolved files
git add <resolved-file>

# 5. Continue rebase
git rebase --continue

# 6. If more conflicts, repeat steps 3-5

# 7. To abort rebase
git rebase --abort

# 8. To skip a problematic commit
git rebase --skip
```

#### Interactive Rebase Conflicts

```bash
# Start interactive rebase
git rebase -i HEAD~3

# In the editor, change 'pick' to:
# - 'edit' to stop at a commit
# - 'squash' to combine with previous
# - 'drop' to remove commit

# After resolving conflicts
git rebase --continue
```

---

## Common Scenarios & Solutions

### Scenario 1: Push Rejected (Non-Fast-Forward)

**Error:** `! [rejected] main -> main (non-fast-forward)`

```bash
# Option 1: Fetch and merge
git fetch origin
git merge origin/main
# Resolve any conflicts
git push origin main

# Option 2: Fetch and rebase (cleaner history)
git fetch origin
git rebase origin/main
# Resolve any conflicts
git push origin main

# Option 3: Force push (CAUTION - use only on personal branches)
git push --force-with-lease origin feature-branch
```

### Scenario 2: Detached HEAD State

**Error:** `You are in 'detached HEAD' state`

```bash
# 1. Create a new branch to save your work
git checkout -b save-my-work

# 2. Or, return to a branch
git checkout main

# 3. If you have uncommitted changes
git stash
git checkout main
git stash pop
```

### Scenario 3: Accidental Commit to Wrong Branch

```bash
# 1. Create a new branch with the commit
git branch correct-branch

# 2. Reset current branch to previous commit
git reset --hard HEAD~1

# 3. Checkout the correct branch
git checkout correct-branch
```

### Scenario 4: Undo Last Commit (Keep Changes)

```bash
# Soft reset - keeps changes staged
git reset --soft HEAD~1

# Mixed reset - keeps changes unstaged
git reset HEAD~1

# Hard reset - discards changes (CAUTION)
git reset --hard HEAD~1
```

### Scenario 5: File Shows as Modified but No Changes

**Cause:** Line ending differences (CRLF vs LF)

```bash
# 1. Configure Git to handle line endings
git config --global core.autocrlf input  # Linux/Mac
git config --global core.autocrlf true   # Windows

# 2. Refresh the index
git rm --cached -r .
git reset --hard

# 3. Create .gitattributes
cat > .gitattributes << 'EOF'
* text=auto
*.sh text eol=lf
*.bat text eol=crlf
EOF
```

### Scenario 6: Large File Rejected

**Error:** `remote: error: File large-file.zip is 150.00 MB; this exceeds GitHub's file size limit`

```bash
# 1. Remove large file from history
git filter-branch --tree-filter 'rm -f large-file.zip' HEAD

# Or use BFG Repo Cleaner (faster)
bfg --delete-files large-file.zip

# 2. Clean up
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# 3. Force push
git push origin main --force

# 4. For future large files, use Git LFS
git lfs install
git lfs track "*.zip"
git add .gitattributes
```

---

## Best Practices

### 1. Pre-Push Checklist

```bash
# Always check status before pushing
git status

# View what will be pushed
git log origin/main..HEAD

# Review changes
git diff origin/main
```

### 2. Use SSH Over HTTPS

SSH authentication is more secure and doesn't require entering credentials repeatedly.

```bash
# Convert existing repo to SSH
git remote set-url origin git@github.com:USERNAME/REPO.git
```

### 3. Keep Credentials Secure

```bash
# Never commit credentials
echo "*.pem" >> .gitignore
echo ".env" >> .gitignore
echo "credentials.json" >> .gitignore
```

### 4. Pull Before Push

```bash
# Always sync before pushing
git pull --rebase origin main
git push origin main
```

### 5. Use Feature Branches

```bash
# Create feature branch
git checkout -b feature/new-feature

# Work on feature
# ...

# Merge back to main
git checkout main
git pull origin main
git merge feature/new-feature
git push origin main
```

### 6. Commit Message Best Practices

```bash
# Use conventional commit format
git commit -m "feat: add user authentication"
git commit -m "fix: resolve merge conflict in config"
git commit -m "docs: update troubleshooting guide"

# Types: feat, fix, docs, style, refactor, test, chore
```

### 7. Configure Git Globally

```bash
# Set identity
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Configure line endings
git config --global core.autocrlf input

# Enable color output
git config --global color.ui auto

# Set default editor
git config --global core.editor "code --wait"
```

---

## Quick Reference Commands

| Issue | Command |
|-------|---------|
| Check remote URL | `git remote -v` |
| Change remote to SSH | `git remote set-url origin git@github.com:USER/REPO.git` |
| Test SSH connection | `ssh -T git@github.com` |
| Check authentication | `gh auth status` |
| Abort merge | `git merge --abort` |
| Abort rebase | `git rebase --abort` |
| View conflict status | `git status` |
| Stash changes | `git stash` |
| Apply stash | `git stash pop` |
| Reset to last commit | `git reset --hard HEAD` |
| View commit history | `git log --oneline -10` |
| Check for uncommitted changes | `git diff --stat` |

---

## Related Resources

- [GitHub Docs: Authenticating with GitHub](https://docs.github.com/en/authentication)
- [GitHub Docs: Resolving Merge Conflicts](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts)
- [Pro Git Book: Git Basics](https://git-scm.com/book/en/v2)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

---

*Document Last Updated: 2026-02-06*
