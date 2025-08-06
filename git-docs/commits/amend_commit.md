### Amending a Previous Commit (N Commits Ago)

1. **Start Interactive Rebase**  

   Begin an interactive rebase targeting the commit to modify (N commits ago):

   ```bash
   # Or --interactive
   git rebase -i HEAD~(N+1)
   ```

   > Note: Use `HEAD~(N+1)` to ensure the `N`th commit is included in the interactive rebase.

2. **Mark Commit for Edit**  

   In the rebase editor, change the word `pick` to `edit` for the commit to modify. Save and exit.

3. **Amend the Commit**  

   Make changes to the files, then amend the commit:

   ```bash
   git add <file>
   git commit --amend
   ```

4. **Continue the Rebase**  

   After amending, continue the rebase:

   ```bash
   git rebase --continue
   ```

### Fixup + Autosquash

1. **Create a fixup commit**

   ```bash
   git commit --fixup <target-commit> -m "fixup: <message>"
   ```

   Where `<target-commit>` is the SHA of the commit we want our work folded into.

2. **Invoke an interactive rebase with autosquash**

   ```bash
   git rebase -i --autosquash <base>
   ```

   * `<base>` can be:

     * `HEAD~N` (to rewrite the last N commits),
     * or the SHA of the commit **just before** the oldest commit in the range. For example, if the history is `a -> b -> c -> d -> e (HEAD)` and we want to rewrite `b`, we could use either

     ```bash
     # By position
     git rebase -i --autosquash HEAD~5
     ```

     or

     ```bash
     # By hash (parent of a):
     git rebase -i --autosquash <hash-of-a>  # (<hash-of-a> is a’s SHA)
     ```

3. **Save & quit the editor**

   Git will automatically turn the fixup into an "autosquash" next to `<target-commit>`.

4. **Finish the rebase**

   ```bash
   git rebase --continue
   ```

   Resolve any conflicts as they arise; afterward the fixup will be squashed into the target commit, and the rest of the commits (`c`, `d`, `e`) replayed unchanged.
