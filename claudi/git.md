# Git Interview Guide and Comparisons

## Basic Concepts

1. **Q: What is Git and how is it different from other VCS?**
   A: Comparison:
   | Feature | Git | SVN | Perforce |
   |---------|-----|-----|----------|
   | Architecture | Distributed | Centralized | Centralized |
   | Speed | Fast (local ops) | Slower (server ops) | Medium |
   | Branching | Lightweight | Heavy | Heavy |
   | Storage | Snapshots | File changes | Changelists |
   | Offline work | Yes | No | Limited |

2. **Q: Explain Git's three tree architecture**
   A: Git's three states:
   - Working Directory: Actual files
   - Staging Area (Index): Prepared changes
   - Repository: Committed changes
   ```bash
   # Working Directory → Staging
   git add file.txt
   
   # Staging → Repository
   git commit -m "message"
   
   # Check status
   git status
   ```

## Basic Commands and Comparisons

1. **Q: Compare git fetch vs git pull**
   A: Differences:
   ```bash
   # git fetch
   - Downloads changes but doesn't merge
   - Safe operation
   git fetch origin
   
   # git pull
   - Downloads and merges changes
   - Equivalent to fetch + merge
   git pull origin master
   # Same as:
   git fetch origin
   git merge origin/master
   ```

2. **Q: Compare merge vs rebase**
   A: Examples:
   ```bash
   # Merge
   git checkout main
   git merge feature
   # Creates merge commit
   # Preserves history
   # Non-destructive
   
   # Rebase
   git checkout feature
   git rebase main
   # Rewrites history
   # Linear history
   # Cleaner history but destructive
   ```

## Branch Management

1. **Q: Different types of branches and strategies**
   A: Common patterns:
   ```bash
   # Feature branch
   git checkout -b feature/user-auth
   
   # Release branch
   git checkout -b release/1.0
   
   # Hotfix branch
   git checkout -b hotfix/security-patch
   
   # Git flow example
   main (production)
   ├── develop
   │   ├── feature/login
   │   └── feature/signup
   ├── release/1.0
   └── hotfix/critical-fix
   ```

2. **Q: Compare different merge strategies**
   A: Examples:
   ```bash
   # Fast-forward merge
   git merge --ff-only feature

   # No-fast-forward
   git merge --no-ff feature

   # Squash merge
   git merge --squash feature
   git commit -m "Feature complete"

   # Rebase and merge
   git checkout feature
   git rebase main
   git checkout main
   git merge feature
   ```

## Advanced Operations

1. **Q: Compare different reset types**
   A: Reset types:
   ```bash
   # Soft reset (keeps changes staged)
   git reset --soft HEAD~1
   
   # Mixed reset (keeps changes unstaged)
   git reset --mixed HEAD~1
   
   # Hard reset (discards changes)
   git reset --hard HEAD~1
   
   # Comparison
   | Type  | Working Dir | Staging | Repository |
   |-------|------------|----------|------------|
   | Soft  | Unchanged  | Kept     | Reset      |
   | Mixed | Unchanged  | Reset    | Reset      |
   | Hard  | Reset      | Reset    | Reset      |
   ```

2. **Q: Compare revert vs reset**
   A: Examples:
   ```bash
   # Revert creates new commit
   git revert abc123
   
   # Reset modifies history
   git reset abc123
   
   # When to use:
   Revert: Public branches
   Reset: Local branches only
   ```

## Conflict Resolution

1. **Q: How to handle merge conflicts?**
   A: Resolution steps:
   ```bash
   # Identify conflicts
   git status
   
   # File with conflicts
   <<<<<<< HEAD
   current changes
   =======
   incoming changes
   >>>>>>> feature
   
   # Resolution options
   1. Manual edit
   2. Use tools:
   git mergetool
   
   3. Choose version:
   git checkout --ours file.txt
   git checkout --theirs file.txt
   
   # After resolution
   git add file.txt
   git commit
   ```

## Advanced Git Features

1. **Q: Compare cherry-pick vs rebase**
   A: Examples:
   ```bash
   # Cherry-pick specific commits
   git cherry-pick abc123 def456
   
   # Interactive rebase
   git rebase -i HEAD~3
   
   # Use cases:
   Cherry-pick: Single commits
   Rebase: Reorganize multiple commits
   ```

2. **Q: Explain Git hooks with examples**
   A: Common hooks:
   ```bash
   # pre-commit hook
   #!/bin/sh
   # .git/hooks/pre-commit
   npm test
   if [ $? -ne 0 ]; then
       echo "Tests must pass before commit!"
       exit 1
   fi
   
   # pre-push hook
   #!/bin/sh
   # .git/hooks/pre-push
   npm run lint
   ```

## Git Internals

1. **Q: Explain Git objects**
   A: Types:
   ```bash
   # Blob: File content
   git hash-object file.txt
   
   # Tree: Directory structure
   git write-tree
   
   # Commit: Snapshot with metadata
   git commit-tree <tree-sha>
   
   # Tag: Named reference
   git tag -a v1.0 -m "Version 1.0"
   ```

2. **Q: Compare different Git storage methods**
   A: Storage types:
   ```bash
   # Pack files vs loose objects
   git gc  # Compress objects
   
   # Shallow clone vs full clone
   git clone --depth 1 url  # Shallow
   git clone url           # Full
   
   # Sparse checkout
   git sparse-checkout set dir1 dir2
   ```

## Common Scenarios and Solutions

1. **Q: How to fix common mistakes?**
   A: Solutions:
   ```bash
   # Undo last commit
   git reset --soft HEAD^
   
   # Remove file from staging
   git restore --staged file.txt
   
   # Recover deleted branch
   git reflog
   git checkout -b recovered-branch <sha>
   
   # Fix wrong commit message
   git commit --amend
   ```

2. **Q: Compare stash operations**
   A: Stash types:
   ```bash
   # Basic stash
   git stash
   
   # Named stash
   git stash save "feature work"
   
   # Partial stash
   git stash -p
   
   # Apply vs Pop
   git stash apply  # Keeps stash
   git stash pop    # Removes stash
   ```

## Advanced Workflows

1. **Q: Compare different Git workflows**
   A: Common workflows:
   ```bash
   # Feature Branch Workflow
   main → feature → main
   
   # Gitflow
   main → develop → feature → develop → release → main
   
   # Trunk-Based Development
   main → short-lived feature → main
   
   # Comparison
   | Workflow    | Complexity | Release Cycle |
   |------------|------------|---------------|
   | Feature    | Low        | Continuous    |
   | Gitflow    | High       | Scheduled     |
   | Trunk-Based| Low        | Continuous    |
   ```

2. **Q: Compare different code review strategies**
   A: Methods:
   ```bash
   # Pull Request Review
   git push origin feature
   # Create PR in platform
   
   # Pre-commit Review
   git diff main...feature
   
   # Post-commit Review
   git log -p main...feature
   ```

## Best Practices

1. **Q: Compare commit message styles**
   A: Examples:
   ```bash
   # Conventional Commits
   feat: add login functionality
   fix: resolve memory leak
   docs: update README
   
   # Karma Style
   feat(auth): add OAuth support
   
   # Custom Format
   [FIX-123] Fix database connection
   ```

2. **Q: Security best practices**
   A: Measures:
   ```bash
   # Signed commits
   git config --global commit.gpgsign true
   
   # Sensitive data
   git-secrets --scan
   
   # Access control
   git config --local core.sharedRepository group
   ```