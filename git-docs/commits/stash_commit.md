### Git Stash: Handy Commands and Use Cases

#### 1. **Stashing Changes**

When there is a need to save local changes temporarily without committing, `git stash` can be used to store those changes and return the working directory to a clean state:

```bash
git stash
```

This saves both tracked and untracked changes, allowing for retrieval later.

#### 2. **Listing All Stashes**

To list all stashes created:

```bash
git stash list
```

Each stash is listed with an index, such as `stash@{0}`, `stash@{1}`, etc., which can be referenced for further actions.

#### 3. **Applying a Stash**

The most recent stash can be applied with:

```bash
git stash apply
```

This re-applies the changes from the stash but leaves the stash in the stash list.

To apply a specific stash (e.g., `stash@{1}`):

```bash
git stash apply stash@{1}
```

For applying and dropping the stash at the same time:

```bash
git stash pop
```

This applies and removes the stash from the list.

#### 4. **Dropping a Stash**

To delete a specific stash after it is no longer needed:

```bash
git stash drop stash@{1}
```

For deleting all stashes:

```bash
git stash clear
```

#### 5. **Stashing Untracked or Ignored Files**

By default, `git stash` stashes only tracked files. To include untracked files:

```bash
git stash -u
```

To stash both untracked and ignored files:

```bash
git stash -a
```

#### 6. **Creating a Named Stash**

A description can be added when stashing to make it easier to identify later:

```bash
git stash push -m "stash description"
```

#### 7. **Applying a Stash in a Different Branch**

A stash can be applied to a different branch by first switching to the target branch:

```bash
git checkout <target-branch>
git stash apply stash@{0}
```

#### 8. **Inspecting a Stash Without Applying**

To view the changes in a stash before applying them:

```bash
git stash show -p stash@{0}
```

This shows the diff of the changes saved in the stash.

#### 9. **Stashing Specific Files**

If only specific files need to be stashed, use `git stash push` and specify the files:

```bash
git stash push <file>
```

#### 10. **Restoring a Stash with Conflicts**

In case there are conflicts when applying a stash, Git will indicate the conflicting files. Resolve the conflicts, then continue:

```bash
git add <conflicted-file>
git stash drop stash@{0}
```
