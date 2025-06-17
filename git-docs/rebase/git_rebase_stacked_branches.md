## Linear Rebase Strategy for Stacked Feature Branches

### Scenario

We have three branches:

* **main**: production-ready base branch
* **feature-base**: foundational patch or utility improvements (branched from `main`)
* **feature-dependent**: feature that builds on `feature-base` but will eventually merge into `main`

We assume **`feature-base`** is reviewed and merged into **`main`** before **`feature-dependent`**.

---

## Goals

1. Develop `feature-dependent` with access to `feature-base` changes
2. Rebase `feature-dependent` onto `main` without duplicating `feature-base` commits
3. Preserve a clean, linear history (no extra merge commits)

---

## Terminology

* **Current**: branch being rebased (`feature-dependent`)
* **Upstream**: original base for rebasing (`feature-base`)
* **Onto**: target base for the new history (`main`)

---

## Workflow Steps

1. **Start from `main`**

   ```bash
   git checkout main
   ```

2. **Create and commit to `feature-base`**

   ```bash
   git checkout -b feature-base
   # Make foundational changes
   git add . && git commit -m "Add foundational utility or patch"
   ```

3. **Create and commit to `feature-dependent`**

   ```bash
   git checkout main
   git checkout -b feature-dependent
   # Initial dependent work
   git add . && git commit -m "Initial work on dependent feature"
   ```

4. **Rebase `feature-dependent` onto `feature-base`**

   ```bash
   git checkout feature-dependent
   git rebase feature-base
   ```

   *Resulting history:*

   ```text
   A --- B      (main)
         \
          C    (feature-base)
           \
            D'---E'  (feature-dependent)
   ```

5. **Merge `feature-base` into `main`**

   ```bash
   git checkout main
   git merge --no-ff feature-base
   ```

   *Now `main` is `A → B → C`.*

6. **Rebase `feature-dependent` onto `main` without duplication**

   ```bash
   git checkout feature-dependent
   git rebase --onto main feature-base
   ```

   * **Upstream**: `feature-base` (commits before or equal to C are excluded)
   * **Onto**: `main` (which already includes C)
   * Only `D'` and `E'` are replayed onto `main` as `D"` and `E"`.

   *Final history:*

   ```text
   A --- B --- C   (main)
               \
                D"---E"   (feature-dependent)
   ```

7. **Verify and push**

   ```bash
   git log --oneline --graph --decorate
   git push origin feature-dependent --force-with-lease
   ```

---

## How `--onto` Works

```bash
git rebase --onto <new-base> <upstream> [<branch>]
```

* **`<new-base>`** (`main`): where to graft the commits
* **`<upstream>`** (`feature-base`): commits up to this point are excluded
* **`<branch>`** (`feature-dependent`, optional if checked out)

Steps Git performs:

1. Finds the merge-base of `feature-dependent` and `feature-base` (commit C).
2. Selects commits after that base (`D'`, `E'`).
3. Replays them onto `main`, producing `D"`, `E"` whose parent is C.

---

## Default Rebase Behavior (Without `--onto`)

### General Default Behavior

When we run `git rebase <upstream>` without the `--onto` option, Git uses a simplified form:

```bash
git rebase <upstream>
# Equivalent to
git rebase --onto <upstream> <upstream> HEAD
```

In this case:

* **Upstream**: the branch we're rebasing onto
* **Onto**: same as upstream (the target destination)
* Git finds the merge-base between our current branch and `<upstream>`
* All commits after that merge-base on our current branch are replayed onto the tip of `<upstream>`

Here, `HEAD` is simply a shorthand for "the commit currently checked out," i.e. the tip of whatever branch we are on, i.e., `feature-dependent`. In other words, `HEAD` tells Git "take the commits from the branch I’m currently on" and replay them.

### In Our Scenario

When we execute:

```bash
git checkout feature-dependent
git rebase feature-base
```

Git performs these steps:

1. **Find merge-base**: Determines the common ancestor between our `feature-dependent` and `feature-base`
   * Initially: merge-base is commit B (from `main`)
   * After our first rebase: merge-base becomes commit C (tip of `feature-base`)

2. **Identify commits to replay**: Selects all commits on our `feature-dependent` that come after the merge-base
   * Initially: commits D, E from our `feature-dependent`
   * After our first rebase: any new commits we added to `feature-dependent`

3. **Replay onto target**: Places those commits on top of the tip of `feature-base` (commit C)

**Result**: Our `feature-dependent` now has the complete history of `feature-base` plus our own commits.

### Why This Differs from `--onto`

The key difference:

* **Default rebase**: Replays commits onto the *tip* of the upstream branch
* **`--onto` rebase**: Replays commits onto a *different* target, excluding upstream commits

This is why we need `--onto` in step 6 - to avoid duplicating the `feature-base` commits that are already in our `main`.

---

## Merge-Base

### Definition

The **merge-base** of two commits is their best common ancestor in the commit graph:

* It’s the shared commit that is not an ancestor of any other shared commit.
* It defines the split point where two branches diverged.

### Identification Algorithm

1. Collect all ancestors of both commits.
2. Intersect the two sets to find common ancestors.
3. Remove any common ancestor that is an ancestor of another common ancestor.
4. The remaining commit(s) are the merge-base.

### Role in Rebase

When rebasing onto an upstream branch, the merge-base:

* Determines which commits on the current branch are *new* (those after the merge-base).
* Ensures only those new commits are replayed onto the target.

---

### Within in Our Scenario

* **`main`**: commits A -> B
* **`feature-base`**: A -> B -> C
* **`feature-dependent`** (after initial rebase): A -> B -> C -> D' -> E'

Rebasing `feature-dependent` onto `feature-base`:

* Merge-base = C
* New commits = D', E'
* Replay D', E' onto C

Rebasing `feature-dependent` onto `main` with `--onto` (after `feature-base` is merged`):

* Merge-base (with `feature-base`) = C
* New commits = D', E'
* Replay onto `main` (A -> B -> C) -> D" -> E"

Rebase asks: “Which commits come *after* C on `feature-dependent`?” and moves only those.
