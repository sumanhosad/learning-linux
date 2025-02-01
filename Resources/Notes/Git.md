# Git Commands Cheat Sheet

## **Basic Git Commands**

### **Initialize a Repository**

- `git init`  
  Initializes a new Git repository in the current directory.

### **Check Repository Status**

- `git status`  
  Shows the current status of files (staged, unstaged, untracked).

### **Add Files to Staging Area**

- `git add <file>`  
  Stages a specific file for the next commit.
- `git add <folder>/`  
  Stages all files in a specific folder.
- `git add .`  
  Stages all changes in the current directory.

### **Create a Commit**

- `git commit -m "commit message"`  
  Commits staged changes with a descriptive message.
- `git commit --amend -m "new commit message"`  
  Updates the previous commit (e.g., to fix a message or add changes).

### **View Commit History**

- `git log`  
  Shows the commit history.
- `git log --all`  
  Shows the commit history for all branches.
- `git log --all --graph`  
  Visualizes branching and merging.

---

## **Branching and Merging**

### **Manage Branches**

- `git branch`  
  Lists all branches.
- `git branch <branch_name>`  
  Creates a new branch.
- `git checkout <branch_name>`  
  Switches to the specified branch.
- `git branch -D <branch_name>`  
  Deletes the specified branch.

### **Merging Branches**

- `git merge <branch_name>`  
  Merges the specified branch into the current branch.
- `git merge <branch_name> -m "merge message"`  
  Adds a custom merge commit message.

### **View Branch History**

- `git log --all --graph`  
  Displays a visual history of all branches.

---

## **Undoing Changes**

### **Unstage Changes**

- `git reset <file>`  
  Removes a specific file from the staging area.
- `git reset .`  
  Removes all files from the staging area.

### **Discard Changes**

- `git checkout -- <file>`  
  Discards changes to a specific file.
- `git checkout -- .`  
  Discards all changes in the working directory.

### **Revert to Previous State**

- `git checkout <commit_hash>`  
  Checks out a specific commit (read-only state).
- `git checkout <commit_hash> <file>`  
  Restores a file to its state in a previous commit.
- `git reset --hard <commit_hash>`  
  Resets the working directory and commit history to a previous commit (destructive).

---

## **Remote Repositories**

### **Manage Remotes**

- `git remote add <remote_name> <url>`  
  Links a remote repository to the local repository.
- `git remote -v`  
  Lists all linked remote repositories.
- `git remote remove <remote_name>`  
  Removes a remote repository link.

### **Push Changes**

- `git push <remote_name> <branch>`  
  Pushes the specified branch to the remote repository.
- `git push --set-upstream <remote_name> <branch>`  
  Sets a default remote branch for the local branch.
- `git push -f`  
  Force-pushes changes to the remote repository (overwrites remote history).

### **Fetch and Pull Changes**

- `git fetch`  
  Updates all remote tracking branches.
- `git pull <remote_name> <branch>`  
  Pulls changes from the remote branch into the current branch.

### **Clone Repositories**

- `git clone <url>`  
  Clones a remote repository to the local machine.
- `git clone <url> <folder_name>`  
  Clones a remote repository into a specific folder.

---

## **Advanced Features**

### **Aliases (Shortcuts)**

- `git config --global alias.<shortcut> <command>`  
  Creates a shortcut for a Git command.
  - Example: `git config --global alias.s "status"` creates the shortcut `git s` for `git status`.

### **Ignore Files**

- `.gitignore`  
  A file that specifies files and folders for Git to ignore.

### **Remove Git from a Project**

- `rm -rf .git`  
  Deletes the Git repository from the current directory.

---

## **Resolving Merge Conflicts**

### **Identify and Resolve Conflicts**

- When a merge conflict occurs, Git marks conflicts with:
  ```
  <<<<<<< HEAD
  Current branch code
  =======
  Merging branch code
  >>>>>>> <branch_name>
  ```
- Steps to resolve:
  1. Open the conflicting files and manually resolve the conflicts.
  2. Remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
  3. Stage the resolved files:
     ```bash
     git add <file>
     ```
  4. Commit the resolution:
     ```bash
     git commit -m "Resolved merge conflicts"
     ```

---

## **Feature Branch Workflow**

1. Create a new feature branch:
   ```bash
   git branch <feature_branch>
   git checkout <feature_branch>
   ```
2. Work on the feature, stage, and commit changes:
   ```bash
   git add .
   git commit -m "Feature implementation"
   ```
3. Push the feature branch to the remote repository:
   ```bash
   git push origin <feature_branch>
   ```
4. Open a pull request on GitHub for code review.
5. After approval, merge the feature branch into the `main` branch.
6. Update your local repository:
   ```bash
   git checkout main
   git pull origin main
   ```
