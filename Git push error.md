# Debugging Diaries - 2: Git Push Error

**📅 Date:** 21-Oct-2025  
**🧑‍💻 Context:** While working on my local Git repository for *github-for-devops-workshop*, I tried to push a new branch to GitHub.

---

## ⚠️ Error Message
```
C:\Users\abhij\OneDrive\Documents\github-for-devops-workshop> git push origin testbranch
error: src refspec testbranch does not match any
error: failed to push some refs to 'https://github.com/abhi00786/github-for-devops-workshop.git'

PS C:\Users\abhij\OneDrive\Documents\github-for-devops-workshop> git push origin feature
error: src refspec feature does not match any
error: failed to push some refs to 'https://github.com/abhi00786/github-for-devops-workshop.git'
```

---

## 🔍 Diagnosis
- The error `src refspec <branch-name> does not match any` indicates that the local branch (`testbranch` or `feature`) **does not exist or has no commits**.  
- `git push` only works if the local branch exists **and** has at least one commit to reference.  
- Common causes:
  1. The branch wasn’t created locally (`git checkout -b branchname` missing).  
  2. The branch exists but has no commits yet.  
  3. A typo in the branch name when pushing.

---

## 🧠 Root Cause
The branch `testbranch` (and later `feature`) had not been created locally before attempting to push.

---

## 🧩 Resolution Steps
1. **Verify existing branches:**
   ```bash
   git branch
   ```
2. **Create and switch to the branch:**
   ```bash
   git checkout -b testbranch
   ```
3. **Add and commit changes:**
   ```bash
   git add .
   git commit -m "Initial commit on testbranch"
   ```
4. **Push the new branch to remote:**
   ```bash
   git push origin testbranch
   ```

---

## ✅ Outcome
After creating and committing to the branch locally, `git push origin testbranch` succeeded, and the branch appeared in the GitHub repository.
