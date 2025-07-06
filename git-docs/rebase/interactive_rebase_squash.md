## Interactive Rebase & Squashing Commits

Interactive rebasing and squashing commits:

1. **Vim-based** workflow (default Git editor)  
2. **VS Code–based** workflow (using `code --wait`); see [StackOverflow answer](https://stackoverflow.com/a/36644561/12923148)

---

## Prerequisites

```bash
# Ensure we’re on the branch we want to rewrite
git checkout feature-branch
```

Use `git log --oneline` to identify how many commits back we need to rebase. For example:

```bash
git log --oneline
# Output:
# abc1234 First commit message
# def5678 Second commit message
# 890ghij Third commit message
# klm1234 This is the commit from which point we want to start squashing
```

---

## 1. Vim-Based Interactive Rebase

### 1.1 Configure Vim as Git’s editor (if needed)

```bash
git config --global core.editor "vim"
```

### 1.2 Start the interactive rebase

Suppose we want to squash the last 3 commits:

```bash
git rebase -i HEAD~3
```

- Opens the Vim editor with a file listing those 3 commits.

### 1.3 Edit the rebase plan

1. **Navigate** to each line:
   - `j` / `↓` to move down  
   - `k` / `↑` to move up  

2. **Enter Insert mode** on the commits you wish to squash:
   - Move the cursor to the word `pick`
   - Press `i`  

3. **Change** `pick` → `squash` (or `s`)

4. **Exit Insert mode**: press `Esc` or `Ctrl + [`

5. **Save & quit**:
   - Type `:wq`  
   - Press `Return`

   ```text
   pick  abc1234 First commit message
   squash def5678 Second commit message
   squash 890ghij Third commit message
   ```

### 1.4 Combine commit messages

- Vim reopens with a combined commit-message buffer.
- Edit the message as desired.
- **Save & quit** again:
  - Type `:wq`
  - Press `Return`

### 1.5 Resolve conflicts (if any)

If Git stops for conflicts:

1. Edit conflicted files.
2. Stage resolved files:

   ```bash
   git add <file>
   ```

3. Continue rebase:

   ```bash
   git rebase --continue
   ```

_or abort if needed:_

```bash
git rebase --abort
```

### 1.6 Verify & push

```bash
git log --oneline --graph
git push origin feature-branch --force-with-lease
```

---

## 2. VS Code–Based Interactive Rebase

### 2.1 Configure VS Code as Git’s editor

```bash
git config --global core.editor "code --wait --new-window"
```

### 2.2 Start the interactive rebase

```bash
git rebase -i HEAD~3
```

- VS Code opens a new window with a file listing the last 3 commits.

### 2.3 Edit the rebase plan in VS Code

1. **Change** each `pick` to `squash` (or `s`) on the commits we want to combine.  
2. **Save** the file: `command + S`  
3. **Close** the tab: `command + W`

### 2.4 Edit combined commit message

- VS Code opens a new tab with the merged commit message.
- Adjust the message as needed.
- **Save** (`command + S`) and **close** (`command + W`).

### 2.5 Resolve conflicts (if any)

If rebase halts for conflicts:

1. **Open** conflicted files.  
2. Use VS Code’s merge markers or the 3-way merge editor.  
3. **Stage** changes in Source Control view or with:

   ```bash
   git add <file>
   ```

4. Continue:

   ```bash
   git rebase --continue
   ```

_or abort:_

```bash
git rebase --abort
```

### 2.6 Verify & push

```bash
git log --oneline --graph
git push origin feature-branch --force-with-lease
```

---

## Quick Reference of Key Commands

| Action                            | Vim                                   | VS Code                  |
|-----------------------------------|---------------------------------------|--------------------------|
| Launch interactive rebase         | `git rebase -i HEAD~N`                | same                     |
| Change `pick` → `squash`          | `i` → edit → `Esc`                    | edit directly            |
| Save & quit editor                | `:wq` + `Return`                      | `command + S`, then `command + W` |
| Continue after conflict resolution| `git rebase --continue`               | same                     |
| Abort rebase                      | `git rebase --abort`                  | same                     |
| Force-push updated branch         | `git push --force-with-lease origin`  | same                     |
