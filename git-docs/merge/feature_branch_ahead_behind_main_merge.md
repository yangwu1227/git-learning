### Merge Feature Branch into `main`

Merging integrates commits from `feature-branch` into `main`, combining changes without rewriting history. Unlike rebasing, merging creates a merge commit to show when the branches were integrated.

### Scenario

- Development is happening on `feature-branch`.
- A teammate has pushed changes to `main` while work is ongoing.

### Step 1: Fetch Latest Changes

Before merging, fetch the latest changes from the remote repository to ensure the local `main` branch is up to date.

```bash
git fetch origin
```

### Step 2: Merge `main` into Feature Branch

To incorporate changes from remote `main` into the feature branch, merge `main` into `feature-branch`. This allows ongoing work to integrate the latest changes from `main` before merging back into `main`.

```bash
git checkout feature-branch
git merge origin/main
```

- **Outcome**: A merge commit is created, integrating remote `main`'s changes with `feature-branch`. This preserves the full history of both branches.

##### Resolving Conflicts

If conflicts arise during the merge, Git will pause and prompt for manual resolution.

1. Resolve the conflicts in the affected files (e.g., using the [3-way merge editor](https://code.visualstudio.com/docs/sourcecontrol/overview#_3way-merge-editor)).

2. After resolving conflicts, stage the resolved files and complete the merge:

   ```bash
   git merge --continue
   ```

To abort the merge (if needed):
```bash
git merge --abort
```

### Step 3: Push Feature Branch

Once the merge is complete, push the updated `feature-branch` to the remote repository.

```bash
git push origin feature-branch
```

### Step 4: Merge Feature Branch into `main`

After ensuring that `feature-branch` has the latest `main` changes integrated, merge the feature branch into `main`. First, switch to `main`, pull the latest changes, and then perform the merge.

```bash
git checkout main
git pull origin main
git merge feature-branch
```

- **Outcome**: A merge commit will be created on `main`, preserving the feature branch's history along with the integrated changes.

### Step 5: Push Merged Changes to `main`

After merging the feature branch into `main`, push the changes to the remote repository.

```bash
git push origin main
```

### Handling Conflicts During the Merge

If conflicts occur during the merge into `main`, the process will pause, and Git will prompt for conflict resolution.

1. **Resolve conflicts** in the affected files.

2. **Stage resolved files** and complete the merge process:

   ```bash
   git merge --continue
   ```

3. Push the changes again after completing the merge and resolving conflicts.
