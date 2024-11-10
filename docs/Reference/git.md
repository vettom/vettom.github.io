# Git

## Tags create/delete

```bash
# Create new tag
git tag -am "Fresh copy" v1.0.0
# Push tag to remote
git push origin v1.0.0
# Delete tag
git tag --delete v1.0.0
# Delete tag remote
git push --delete origin v1.0.0
# Create branch from tag
git checkout -b new-branch v1.0.0

```

## Git delete all commit history
```bash
# Checkout main branch and then create orphan branch
git checkout --orphan temp_branch

#Add all files and commit
git add -A ; git commit -m "Initial commit"

# Delete main branch and rename current branch
git branch -D main ; git branch -m main

# Push changes
git push --force origin main 

```

## Git config updates
```bash
# Get all configuration values
git config -l

# Check username and email set for current repo
git config user.email ; git config user.name

# Set git username and email
git config user.email denny@vettom.com; git config user.name "Denny Vettom"

# Configure git to use specific ssh key
git config --global core.sshCommand "ssh -i ~/.ssh/git_id_rsa -F /dev/null"
```