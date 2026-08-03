# Git Rebase Practice — Day Session

This repository records a learning session focused on creating branches, committing changes, and using git rebase to integrate work. The shell history shown in the repository was used to build this README; below you'll find a line-by-line explanation of each command from the session, why it was run, what it does, common variations, and safety tips.

## Goal of this repo

- Demonstrate a simple feature-branch workflow
- Show how to rebase a feature branch onto main and resolve rebase conflicts
- Explain commands used during a short interactive session so you can reproduce and learn safely

---

## Session commands explained (ordered and grouped)

1) git init
   - What it does: Creates a new Git repository in the current directory by creating a `.git/` folder and initial metadata.
   - When to use: When starting a new project or converting an existing directory into a Git repo.
   - Notes: After `git init` you should create an initial commit (or at least add files) and optionally set the remote with `git remote add origin <url>`.

2) git branch -m main
   - What it does: Renames the current branch to `main` (the `-m` flag performs a move/rename).
   - Why used: Many older Git installs start with `master`; `git branch -m main` renames it to `main`.
   - Variation: `git branch -M main` forces the rename even if `main` already exists. Use with care.

3) clear
   - Shell utility to clear the terminal screen. Not a Git command.

4) echo "app initialized" >> app.txt
   - Appends the text `app initialized` to `app.txt`. If the file doesn't exist, it will be created.
   - `>>` appends; use `>` to overwrite.

5) git add app.txt
   - Stages `app.txt` so it will be part of the next commit. Only staged changes are recorded by `git commit`.

6) git commit -m "chore:initialize"
   - Creates a commit from the staged changes with message `chore:initialize`.
   - Tip: Use clear, conventional commit messages like `chore:`, `feat:`, `fix:` to communicate intent.

7) echo "added header" >> app.txt
   - Appends `added header` to `app.txt`.

8) git commit -am "feat:added header"
   - `git commit -am` stages and commits changes to files that are already tracked by Git (it does not add new, untracked files).
   - The `-a` flag saves a step (no explicit `git add`), but will not include new files — only modifications and deletions of tracked files.

9) echo "added footer" >> app.txt
   - Appends `added footer` to `app.txt`.

10) git commit -am "feat:added footer"
    - Another quick commit using `-a`.

11) clear
    - Clear terminal output.

12) git log --oneline
    - Shows the recent commits in a compact one-line-per-commit view (short SHA + message).
    - Useful for a quick history check.

13) git checkout -b feat/login
    - Creates a new branch named `feat/login` and switches to it immediately.
    - Modern alternative: `git switch -c feat/login` (clearer semantics: `switch -c` creates and switches).

14) git switch main
    - Switches back to the `main` branch. Preferred over `git checkout main` in modern Git versions for clarity.

15) echo "Nitin is mastering devops" >> nit.txt
    - Creates/appends a new file `nit.txt` with some content.

16) git add nit.txt
    - Stages the new file so it can be committed.

17) git commit -m "feat: added description"
    - Commits the new `nit.txt` file to the current branch (`main` in this case).

18) git switch feat/login
    - Switch back to the feature branch to continue work there.

19) git log --oneline --all --graph
    - Shows a compact, graphical view of all refs (branches), and helps visualize where `main` and `feat/login` diverge.

20) echo "Nitin is learning this from cwvj" >> nit.txt
    - Modifies `nit.txt` on the feature branch. Because `nit.txt` was committed on `main`, this modification will create a different commit on `feat/login` when committed.

21) git add nit.txt
    - Stage the change on the feature branch.

22) git commit -m "feat:add info about cwvj"
    - Commit the change on `feat/login`.

23) ls
    - List files in the working directory (not a Git command). Helpful to inspect the project structure.

24) git log --oneline --all --graph
    - Re-check the commit graph. You should now see commits on both `main` and `feat/login`.

25) echo "added layout" >> app.txt
    - Append another change to `app.txt` while on `feat/login`.

26) git add app.txt
    - Stage the `app.txt` change.

27) git commit -m "feat:added layout"
    - Commit the change on `feat/login`.

28) git log --oneline --all --graph
    - Inspect the history graph again. At this point, `feat/login` has commits not present in `main` (and `main` may have commits not on `feat/login`).

29) git rebase main
    - What it does: Replays the commits from the current branch (`feat/login`) on top of `main`.
    - Why: This creates a linear history as if `feat/login` was developed starting from the latest `main`. It makes the history cleaner than a merge commit for small feature branches.
    - What you should know: If `main` has changes that touch the same files as commits on `feat/login`, you may get conflicts during the rebase.

30) git rebase --continue
    - Used after resolving conflicts to tell Git to continue applying the remaining commits.
    - Typical rebase sequence on conflict:
      - Git stops and shows conflicts.  
      - Edit files to resolve conflicts.  
      - Stage resolved files with `git add <file>`.  
      - Run `git rebase --continue` to proceed.

31) git status
    - Shows the current status of the working tree and whether the rebase is in progress, which files are staged, and what Git expects next.

32) git add nit.txt
    - Stages the resolved `nit.txt` after a conflict. This is required before `git rebase --continue`.

33) git rebase --continue
    - Continue the rebase after staging conflict resolutions.

34) clear
    - Clear terminal.

35) git log --oneline --graph
    - View the commit history after a successful rebase. Commits from `feat/login` should now appear on top of `main`'s commits (linear history).

36) cat nit.txt
    - Show the contents of `nit.txt` to verify the expected final text after the rebase and conflict resolution.

37) cat app.txt
    - Show the contents of `app.txt` for verification.

38) git switch main
    - Switch back to `main` to integrate the feature branch.

39) cat app.txt
    - Verify the content of `app.txt` on `main` — before merging it may differ from the rebased feature branch.

40) git merge feat/login
    - Merge the `feat/login` branch into `main`.
    - Because `feat/login` was rebased onto `main`, this merge should be a fast-forward (no merge commit) — `main` pointer will move forward to include the feature commits.
    - If you want to keep a record of the branch with a merge commit instead, avoid rebasing and perform a regular `git merge feat/login` from `main` without rebasing.

41) cat app.txt
    - Final verification that the merged changes are present in the working tree.

---

## Rebase vs Merge: short guidance

- Rebase rewrites commit history by replaying commits onto a different base. It produces a linear history and is useful for local cleanup before sharing.
- Merge creates a merge commit that preserves the exact branching history and is preferred when collaborating on shared branches or when you want to preserve merge context.
- NEVER rebase commits that have already been pushed and shared with others unless everyone coordinating on that branch agrees — rebasing shared history rewrites commits and makes other people's clones diverge.

## Resolving conflicts during rebase

1. Git stops and marks conflicting files.
2. Open each conflicting file, look for conflict markers `<<<<<<<`, `=======`, `>>>>>>>` and decide what the final file should contain.
3. Save the file and run `git add <file>` for each resolved file.
4. Run `git rebase --continue` to proceed. If more conflicts appear, repeat.
5. If you want to abort the rebase and return to the original branch state: `git rebase --abort`.

## Helpful commands to inspect differences

- `git log --oneline --graph --decorate --all` — full but compact visual history.
- `git diff main..feat/login` — shows changes on `feat/login` that are not in `main`.
- `git diff origin/main..main` — compare local main to remote main.

## Safety tips and best practices

- Make frequent commits with clear messages.
- Use feature branches for isolated work (e.g., `feat/login`).
- Consider rebasing feature branches locally to keep history tidy, but avoid rebasing public/shared commits.
- Always run `git status` and `git log --oneline --graph` when you are uncertain.
- Back up important work before destructive operations (`git reset --hard`, deleting `.git`, forced pushes).

## Appendix: minimal reproduction of the workflow (commands)

# initialize repo and set main branch
git init
git branch -m main

# create initial file and commit
echo "app initialized" >> app.txt
git add app.txt
git commit -m "chore:initialize"

# quick commits
echo "added header" >> app.txt
git commit -am "feat:added header"

echo "added footer" >> app.txt
git commit -am "feat:added footer"

# create feature branch and add changes
git checkout -b feat/login
# switch back to main and add a file there
git switch main
echo "Nitin is mastering devops" >> nit.txt
git add nit.txt
git commit -m "feat: added description"

# continue work on feature branch
git switch feat/login
echo "Nitin is learning this from cwvj" >> nit.txt
git add nit.txt
git commit -m "feat:add info about cwvj"

echo "added layout" >> app.txt
git add app.txt
git commit -m "feat:added layout"

# rebase feature onto main, resolve conflicts, continue
git rebase main
# resolve conflicts, git add <file>, then:
git rebase --continue

# verify, then merge into main (fast-forward after rebase)
git switch main
git merge feat/login

---

If you'd like, I can:
- Add the raw recorded history as an appendix in the README, or
- Create a short cheat-sheet file (CHEATSHEET.md) with only commands, or
- Add examples showing typical conflict markers and how to resolve them.

Tell me which of those you'd like next and I will add it.