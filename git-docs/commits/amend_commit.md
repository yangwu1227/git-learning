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
