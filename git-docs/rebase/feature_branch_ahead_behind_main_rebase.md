### Rebase Feature Branch on `main`

Rebasing reapplies commits from `feature-branch` on top of `main`, resulting in a linear history.

### Scenario

- Development is happening on `feature-branch`.
- A teammate has pushed changes to `main` while work is ongoing.

### Step 1: Fetch Latest Changes

Before starting the rebase, fetch the latest changes from the remote repository to ensure the local `main` branch is up to date.

```bash
git fetch origin
```

### Step 2: Rebase Feature Branch onto `main`

Rebase the commits from `feature-branch` on top of the latest `main` branch. This moves the feature branch's commits to the top of the updated `main` branch history.

```bash
git checkout feature-branch
git rebase origin/main
```

- **Outcome**: Commits from `feature-branch` are reapplied on top of the latest changes in `main`. This avoids creating a merge commit, resulting in a linear history.

##### Resolving Conflicts

If conflicts arise during the rebase, Git will pause and prompt for manual resolution.

1. Resolve the conflicts in the affected files (e.g., using the [3-way merge editor](https://code.visualstudio.com/docs/sourcecontrol/overview#_3way-merge-editor)).

2. After resolving conflicts, stage the resolved files and continue the rebase:

   ```bash
   git add <file>
   git rebase --continue
   ```

To abort the rebase (if needed):
```bash
git rebase --abort
```

### Step 3: Push Changes

Since the rebase rewrites the commit history, a forced push is necessary to update the remote `feature-branch`. Use `--force-with-lease` to prevent overwriting any unexpected changes made to the remote branch after the last fetch.

```bash
git push --force-with-lease origin feature-branch
```

**Note**: `--force-with-lease` ensures that only the local changes are force-pushed, without clobbering any changes pushed by others in the meantime.

#### Handling `--force-with-lease` Failure

If `--force-with-lease` fails, it means that new changes were pushed to the remote `feature-branch` after the rebase process began. To resolve this:

1. **Fetch the latest changes** from the remote repository:

   ```bash
   git fetch origin
   ```

2. **Rebase** the local branch on the top of the remote feature-branch with new commits that don't exist in the local branch:

   ```bash
   git rebase origin/feature-branch
   ```

3. Resolve any conflicts if necessary, following the conflict resolution steps outlined above.

4. After completing the rebase, **push the changes again**:

   ```bash
   git push --force-with-lease origin feature-branch
   ```
