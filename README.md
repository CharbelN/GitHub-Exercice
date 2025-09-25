
# Project Title

A brief description of what this project does and who it's for

# 📘 Git Exercises

This document contains my worked answers to the Git exercises.  

---

## 📑 Table of Contents
- [Exercise 1 – Tracking Files](#-exercise-1--tracking-files)
- [Exercise 2 – Branches](#-exercise-2--branches)
- [Exercise 3 – Undoing Changes](#-exercise-3--undoing-changes)
- [Exercise 4 – Rebase](#-exercise-4--rebase)

---

## 🚀 Exercise 1 – Tracking Files

### 1–3. Init and `.git` folder
```bash
mkdir mlops
cd mlops
git init
```
After doing git init we got a .git folder.
This folder is what makes our project a Git repository. It contains all the structure and files Git needs to track the history of the project.
It stores: commit history, branches.

Without this folder, the project is just a normal folder of files, not version-controlled.
---

### 4–6. Add README.md
```bash
touch README.md
git status
git add .
```
Before add → README.md is untracked.
After add → README.md is now in the staging area.
---

### 7. First Commit
```bash
git commit -m "1- Adding README.MD file to the repository"
```
---

### 8–9. Add src directory and files
```bash
mkdir src
cd src
touch file1.py file2.py
cd ..
git add src
git status
git commit -m "2- adding src folder and its internal files"
```
---

### 10–12. Modifying and diff
```bash
echo 'print("1- First change")' >> src/file1.py
git diff
```

Output:
```bash
diff --git a/src/file1.py b/src/file1.py
index e69de29..1eaa088 100644
--- a/src/file1.py
+++ b/src/file1.py
@@ -0,0 +1 @@
+print("1- First change")
```
Next:
```bash
git add .
git diff    # nothing now
```
git diff shows working vs staging. After staging, nothing to show.

Add another change:
```bash
echo 'print("2- Second change")' >> src/file1.py
git diff
git diff --staged
```
git diff → unstaged changes

git diff --staged → staged changes
---

### 13. Log
```bash
git log --oneline --graph --decorate --all --color
```
Output:
```markdown
* a254f87 (HEAD -> master) 3- Updating file1.py with the second round of changes
* 8b5df80 2- Adding file1.py and file2.py
* 7a4e116 1- Adding README.MD file to the repository
```
---

### 14. Show Commit
```bash
git show HEAD
git show a254   # 4 chars minimum
```
---

### 15. Add new file file3.py
```bash
echo 'print("1- First changes in the new file file3.py")' >> src/file3.py
git add .
git commit -m "4- Adding an extra file named file3.py with a small print"
```
---

### Stretch Tasks – Deleting files
With Git
```bash
git rm src/file3.py
```
→ Git deletes from working dir and stages deletion.

With OS
```bash
rm src/file2.py
git status
```
Output:
```yaml
Changes to be committed:
  deleted:    src/file3.py

Changes not staged for commit:
  deleted:    src/file2.py
```
Explanation:
git rm → deletes + stages
rm → deletes but leaves unstaged

Commit both:
```bash
git add .
git commit -m "deleting file2.py and file3.py"
```
---
Stretch Task – Renaming
Rename with Git
```bash
git mv src/file1.py src/file2.py
git status
```
Output:
```bash
renamed: src/file1.py -> src/file2.py
```

Commit:
```bash
git commit -m "Rename file1.py to file2.py"
```

Rename with OS
```bash
mv src/file2.py src/file3.py
```
→ Git thinks file2 deleted, file3 untracked ❌

Fix by staging:
```bash
git add .
git status
```

Output:
```bash
renamed: src/file2.py -> src/file3.py
```

Commit:
```bash
git commit -m "7- Rename file2.py to file3.py"
```

```bash
git log -3
```
git show --stat provides summary of files and changes.
git diff A..B = all changes between commit A and commit B.
---
---

## 🚀 Exercise 2 – Branches
### 1–3. Create and switch branch
```bash
git status
git branch my_first_branch
git checkout my_first_branch
```
---

### 4–5. Add change in branch
```bash
echo 'print("1- First changes in the new file file2.py")' >> src/file2.py
git add .
```

Check log:
```bash
git log --oneline
```

Output:
```bash
b716af4 (HEAD -> my_first_branch) 8- adding file2.py and modifying the file file3.py
fdfcea6 (master) 7- Rename file2.py to file3.py
6e9235a 6- Renaming file1.py to file2.py
22ca867 5- deleting file2.py and file2.py
```
---

### 8. Merge branch into master
```bash
git commit -m "8- adding file2.py"
git checkout master
git merge my_first_branch
git log
```
---

### 9–12. Diverging changes in both branches
```bash
git checkout my_first_branch 
echo 'print("2- Second change in file2.py in my_first_branch")' >> src/file2.py
git add .
git commit -m '9- editing file2.py'

git checkout master
echo 'print("4- Fourth change in file3.py")' >> src/file3.py
git add .
git commit -m '10- editing file3.py'
git merge my_first_branch
```
```bash
Merge made by the 'ort' strategy.
 src/file2.py | 1 +
 1 file changed, 1 insertion(+)
```
---

### Stretch Task – Merge conflict (same line modified)
1- On my_first_branch:
```bash
git checkout my_first_branch
echo 'print("Making changes in branch my_first_branch")' >> src/file2.py
git add .
git commit -m "11- doing changes on line 3 in file2.py"
```

2-On master:
```bash
git checkout master
echo 'print("Making changes in branch master")' >> src/file2.py
git add .
git commit -m "12- doing changes on line 3 in file2.py"
git merge my_first_branch
```
Conflict appears:
```bash
CONFLICT (content): Merge conflict in src/file2.py
```
I resolved it manually by keeping both changes (removed conflict markers, kept both prints), then did git add . + git commit
✅ Merge was successful.

---

9- a. moved to my_first_branch
   b. deleted everything and kept .git 
   c. moved to master and all the deleted files are present

---

10-
```bash
git checkout my_first_branch
echo 'print("4- Fourth change")' >> src/file2.py
git add .
git commit -m "16- editing file2"

git checkout master
git rm src/file2.py
git commit -m "16- deleting file2"
git merge my_first_branch
```

Conflict appears:
```bash
CONFLICT (modify/delete): src/file2.py deleted in HEAD and modified in my_first_branch.
Version my_first_branch of src/file2.py left in tree.
Automatic merge failed; fix conflicts and then commit the result.
```

Resolution: I decided to keep the file
```bash
git checkout my_first_branch src/file2.py
git commit -m "17- fixing the conflict by getting the file2 from the my_first_branch"
```

- To add a custom message when merging:
```bash
git merge -m "custom merge message" branch
```

- To prevent auto-commit on merge:
```bash
git merge --no-commit branch
```

## 🔄 Exercise 3 – Undoing Changes

### 1–2. Create a new branch
```bash
git checkout -b undoing-changes
```

---

### 3. Rename and create files
Since file3.py already existed, I first renamed it to file1.py, then created a new file3.py:
```bash
git mv src/file3.py src/file1.py
echo 'print("1- First change in file3.py")' >> src/file3.py
git add .
git commit -m "18- creating a new file named file3.py"
```

Make another change:
```bash
echo 'print("2- Second change in file3.py")' >> src/file3.py
git diff
```

Undo changes:
```bash
git checkout -- src/file3.py
```
➡️ This discards local modifications and restores the file to the last committed state.

Alternative:
```bash
git restore src/file3.py
```

---

### 7. Add a new file and test revert
```bash
echo 'print("1- First change in file4.py")' >> src/file4.py
git add .
git commit -m "19- adding file4.py"
```
Undo this commit
```bash
git revert HEAD
```
- git checkout → discards uncommitted changes.

- git revert → creates a new commit that undoes the last commit.

---

### 9. Reset with --soft

Make two changes:
```bash
echo 'print("2- Second change in file3.py")' >> src/file3.py
echo 'print("1- First change in file4.py")' >> src/file4.py
git add .
git commit -m "20- committing changes in file3 and file4"
```

Undo last commit but keep the changes staged:
```bash
git reset --soft HEAD^
```

➡️ The branch pointer moves back, commit is removed, but the changes remain staged.
Alternative: git revert HEAD (safer, keeps commit history).

---

### 11. Reset with --mixed (default)
```bash
git reset HEAD
```
➡️ This unstages the changes but keeps them in the working directory.

We used HEAD (not HEAD^) → only clear the staging area.

Default mode is --mixed.

---

### 12. Reset with --hard
```bash
git reset --hard
```
➡️ Removes the last commit and discards all changes in working directory and staging area.
To recover, use git reflog.

---

### 14. Add a new file
```bash
echo 'print("1- First change in file5.py")' >> src/file5.py
git add .
git commit -m "20- adding file5.py"
```

---

## Stretch Task
### 1-2. Undo file changes with checkout
```bash
echo 'print("2- Second change in file5.py")' >> src/file5.py
git checkout -- src/file5.py
```
- File is reset to last committed state.

- Removes unstaged changes.

- HEAD pointer unchanged.

---

### 3. Revert multiple commits
```bash
echo 'print("2- Second change in file5.py")' >> src/file5.py
git add .
git commit -m "20- modifying file5"

echo 'print("2- Second change in file4.py")' >> src/file4.py
git add .
git commit -m "21- modifying file4"

git revert HEAD~2..HEAD
```
➡️ Effects:

- No commits deleted.

- Two new commits are created that undo the last two.

- Range HEAD~2..HEAD = last two commits.

- Ranges not valid for git reset.

### 6. Anchor branch and reset testing
```bash
git branch anchor
git reset HEAD~3
```

➡️ Moves branch pointer back 3 commits.
Commits lost from history, but changes remain unstaged.

To recover:
```bash
git reset --hard anchor
```

### Reset scope tests
Soft:
```bash
git reset --soft anchor
```
HEAD → anchor, changes staged, working directory intact.

---

Mixed (default):
```bash
git reset anchor
```
HEAD → anchor, changes unstaged, working directory intact.

---

Hard:
```bash
git reset --hard anchor
```
HEAD → anchor, staging + working directory wiped.

---

## 🚀 Exercise 4 – Rebase
### 1. Create a feature branch
```bash
git checkout -b feature-branch
mkdir feature
echo 'print("1- First change in feature1.py")' >> feature/feature1.py
git add .
git commit -m "Add feature1.py"
```
### 2. Create changes in main branch
```bash
git checkout master
git checkout -b main
echo 'print("1- First change in main.py")' >> main.py
git add .
git commit -m "Add main.py"
```

### 3. Rebase feature branch onto main
```bash
git checkout feature-branch
git rebase main
```
➡️ What rebase does:

- Takes commits from the current branch (feature-branch).

- Replays them on top of the target branch (main).

- main is the new base.

Changes:
- The commit from feature-branch (Add feature1.py) was re-applied on top of main.

- feature-branch now has a new commit ID (rebasing rewrites history).

- The history is now linear, no branch split.

### 5. Merge vs Rebase - Key differences
Merge

- Combines histories with a merge commit.

- Keeps branching structure visible.

- Non-linear history.

Rebase

- Replays commits onto the target branch.

- No merge commit is created.

- Produces a clean, linear history.

- Commit hashes change (rewrites history).