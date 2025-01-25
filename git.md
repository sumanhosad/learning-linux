---
# Git Branching and Merging

Git branching and merging are essential features for managing parallel development, allowing developers to work on features, bug fixes, or experiments in isolated environments without affecting the main codebase.
---

## **1. What is a Branch?**

A branch in Git is a lightweight, movable pointer to a commit. By default, Git uses the `main` (or `master`) branch. Branches enable you to:

- Work on new features independently.
- Fix bugs without disrupting the stable codebase.
- Experiment with changes.

### Viewing Branches

To see all branches in the repository:

```bash
git branch
```

### Creating a New Branch

```bash
git branch <branch_name>
```

### Switching Between Branches

```bash
git checkout <branch_name>
```

OR, in modern Git versions:

```bash
git switch <branch_name>
```

### Create and Switch to a New Branch

```bash
git checkout -b <branch_name>
```

OR:

```bash
git switch -c <branch_name>
```

---

## **2. Merging Branches**

Merging is the process of integrating changes from one branch into another.

### Fast-Forward Merge

If the current branch has no new commits since the branch you are merging, Git performs a fast-forward merge:

```bash
git merge <branch_name>
```

### Three-Way Merge

If both branches have new commits, Git performs a three-way merge, creating a new merge commit:

```bash
git merge <branch_name>
```

### Resolving Merge Conflicts

If Git encounters conflicting changes during the merge, it will stop and mark the conflicts in the affected files. To resolve:

1. Open the files with conflicts.
2. Look for conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) and edit as needed.
3. Mark the conflicts as resolved:
   ```bash
   git add <file_name>
   ```
4. Complete the merge:
   ```bash
   git commit
   ```

---

## **3. Deleting Branches**

After merging a branch, you can safely delete it to keep the repository clean.

### Delete a Local Branch

```bash
git branch -d <branch_name>
```

Use `-D` to force delete a branch:

```bash
git branch -D <branch_name>
```

### Delete a Remote Branch

```bash
git push origin --delete <branch_name>
```

---

## **4. Best Practices for Branching and Merging**

1. **Use Descriptive Branch Names**:

   - Example: `feature/login-page`, `bugfix/crash-on-startup`, or `hotfix/version-1.0.1`.

2. **Keep Branches Small and Focused**:

   - Avoid working on multiple features in the same branch.

3. **Regularly Sync with `main`**:

   - Pull changes from the main branch frequently to minimize merge conflicts:
     ```bash
     git pull origin main
     ```

4. **Review Code Before Merging**:

   - Use pull requests or merge requests for code reviews.

5. **Delete Merged Branches**:
   - Remove branches after merging to reduce clutter.

---

## **5. Example: Branching and Merging Workflow**

### Step 1: Create and Switch to a New Branch

```bash
git branch feature/new-ui
git switch feature/new-ui
```

### Step 2: Make Changes and Commit

```bash
# Edit files and add changes
git add .
git commit -m "Add new UI feature"
```

### Step 3: Merge the Feature Branch into Main

1. Switch to the main branch:
   ```bash
   git switch main
   ```
2. Merge the feature branch:
   ```bash
   git merge feature/new-ui
   ```
3. Delete the feature branch:
   ```bash
   git branch -d feature/new-ui
   ```

---

## **6. Additional Commands**

- **List Remote Branches**:

  ```bash
  git branch -r
  ```

- **Push a Local Branch to Remote**:

  ```bash
  git push origin <branch_name>
  ```

- **Set Upstream for a Branch**:
  ```bash
  git push -u origin <branch_name>
  ```

---
