### **Basic Git Commands & Concepts**

#### **1. `git pull` vs `git fetch`**
- **`git fetch`**: Downloads updates from the remote repository but does not automatically merge them into the local branch. It updates your local references.
- **`git pull`**: A combination of `git fetch` followed by a `git merge`. It fetches and merges the remote changes into your current branch.

**Example**:
- **`git fetch`**:
  ```bash
  git fetch origin
  ```
- **`git pull`**:
  ```bash
  git pull origin main
  ```

---

#### **2. `git reset` vs `git revert`**
- **`git reset`**: Moves the HEAD pointer to a previous commit, effectively removing commits and optionally modifying the staging area.
- **`git revert`**: Creates a new commit that undoes the changes of a specific previous commit. This is safer for shared repositories as it doesn't alter history.

**Example**:
- **`git reset`**:
  ```bash
  git reset --hard HEAD~1
  ```
- **`git revert`**:
  ```bash
  git revert <commit-hash>
  ```

---

#### **3. `git cherry-pick`**
- **`git cherry-pick`**: Allows you to apply the changes from a single commit from another branch to your current branch without merging the entire branch.

**Example**:
- Apply a commit with the hash `abc123` to the current branch:
  ```bash
  git cherry-pick abc123
  ```

---

#### **4. `git commit --amend`**
- **`git commit --amend`**: Modifies the most recent commit. It can be used to add changes or modify the commit message of the last commit.

**Example**:
- To add new changes to the last commit or change the commit message:
  ```bash
  git add <file>
  git commit --amend
  ```

---

#### **5. `git stash`**
- **`git stash`**: Temporarily saves changes that you are not ready to commit yet. It allows you to work on other tasks and return to your changes later.

**Example**:
- Stash your current changes:
  ```bash
  git stash
  ```
- Apply the stashed changes:
  ```bash
  git stash pop
  ```

---

#### **6. `git rebase` vs `git merge`**
- **`git rebase`**: Re-applies commits from the current branch onto another branch, creating a linear history.
- **`git merge`**: Combines two branches, preserving both branch histories and creating a merge commit.

**Example**:
- **Rebase**:
  ```bash
  git checkout feature-branch
  git rebase main
  ```
- **Merge**:
  ```bash
  git checkout main
  git merge feature-branch
  ```

---

### **Scenario-Based Git Questions**

---

#### **1. Scenario: Applying a commit from another branch.**
   - **Question**: How do I apply a specific commit from another branch without merging the whole branch?
   - **Answer**: Use `git cherry-pick` to apply that commit to your current branch.
     ```bash
     git cherry-pick <commit-hash>
     ```

---

#### **2. Scenario: Undoing the last commit but keeping the changes.**
   - **Question**: How can I undo my last commit but keep the changes locally?
   - **Answer**: Use `git reset --soft HEAD~1` to undo the last commit but keep the changes staged.
     ```bash
     git reset --soft HEAD~1
     ```

---

#### **3. Scenario: Recovering a lost commit.**
   - **Question**: How do I recover a commit that is no longer in the history?
   - **Answer**: Use `git reflog` to track the history of HEAD and recover lost commits.
     ```bash
     git reflog
     git checkout <commit-hash>
     ```

---

#### **4. Scenario: Changing the last commit message.**
   - **Question**: How can I change the commit message of the last commit?
   - **Answer**: Use `git commit --amend` to modify the message of the last commit.
     ```bash
     git commit --amend
     ```

---

#### **5. Scenario: Recovering a deleted branch.**
   - **Question**: How do I recover a branch that I deleted?
   - **Answer**: If you know the commit hash, you can create a new branch from it.
     ```bash
     git checkout -b <branch-name> <commit-hash>
     ```

---

#### **6. Scenario: Moving changes to the correct branch.**
   - **Question**: How can I move uncommitted changes from one branch to another?
   - **Answer**: Use `git stash` to save your changes, switch to the correct branch, and then apply the stash.
     ```bash
     git stash
     git checkout <correct-branch>
     git stash pop
     ```

---

### **Advanced Git Concepts**

---

#### **1. `git reset` vs `git checkout`**
- **`git reset`**: Moves HEAD to a specific commit and optionally modifies the index and working directory.
- **`git checkout`**: Used for switching branches or restoring files in the working directory.

**Example**:
- **`git reset`**:
  ```bash
  git reset --hard HEAD~1
  ```
- **`git checkout`**:
  ```bash
  git checkout <branch-name>
  git checkout -- <file>
  ```

---

#### **2. `git diff`**
- **`git diff`**: Shows the changes between commits, the working directory, and the staging area.

**Example**:
- To show the difference between the working directory and the staging area:
  ```bash
  git diff
  ```

---

#### **3. `git log` and Customizing Logs**
- **`git log`**: Displays the commit history.
- You can use various flags to customize the output.

**Example**:
- To show the log with a specific format:
  ```bash
  git log --oneline --graph --decorate --all
  ```

---

#### **4. `git log --stat`**
- **`git log --stat`**: Shows the commit history along with file modifications and the number of changes made in each commit.

**Example**:
  ```bash
  git log --stat
  ```

---

### **Git Permission & Access Control Questions**

---

#### **1. How to change file permissions in Git?**
   - **Answer**: Git doesn’t track file permissions by default (except for executable bit). You can modify file permissions and commit the changes.
     - To make a file executable:
       ```bash
       chmod +x <file>
       git add <file>
       git commit -m "Make file executable"
       ```

---

#### **2. How to manage Git access control?**
   - **Answer**: Git uses SSH keys for authentication when accessing remote repositories. Make sure your SSH key is added to your Git provider (GitHub, GitLab, etc.).
     - To add your SSH key:
       ```bash
       ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
       ssh-add ~/.ssh/id_rsa
       ```

---

### **Common Git Troubleshooting Scenarios**

---

#### **1. Git merge conflicts**
   - **Problem**: During a merge or rebase, Git cannot automatically combine changes from different branches.
   - **Solution**: Manually resolve the conflict in the files, then:
     ```bash
     git add <resolved-file>
     git commit
     ```

---

#### **2. Pushing after a forced reset**
   - **Problem**: You have reset or amended commits locally, but pushing to the remote repository fails due to non-fast-forward errors.
   - **Solution**: Use `git push --force` to overwrite the remote branch.
     ```bash
     git push --force
     ```

---

#### **3. Recovering deleted files**
   - **Problem**: You accidentally deleted a file from the Git repository.
   - **Solution**: Use `git checkout` to recover a file from a previous commit.
     ```bash
     git checkout HEAD~1 -- <file>
     ```

---

### **Comparisons**

#### **1. `git pull` vs `git fetch`**
- **`git fetch`**: Downloads updates but doesn’t merge them.
- **`git pull`**: Fetches and merges updates into your current branch.

#### **2. `git reset` vs `git revert`**
- **`git reset`**: Rewrites history and can affect your working directory.
- **`git revert`**: Creates a new commit that undoes changes but does not alter history.

---

This comprehensive guide combines common Git commands, comparisons, scenario-based solutions, and practical usage examples to help you prepare for any Git-related interview question effectively.
