# Git notes

## Fundamentals

- git nav is all about commit hashes. Most things in git are just pointers to commit hashes (like branches)
- `HEAD` is just where you're at now
- git configs have several levels of granularity, each more specific level overrides higher level. So local overrides global, etc. This is nice for project-specifics configs
- some fun nesting you can do with .gitignores
- `fetch` downloads objects and refs from another repo. It doesn't pull the commit history or anything though
- `pull` by default fetches and merges with the remote branch. You can change this to rebase instead
- `Merge` combines branches by creating a merge commit; keeps the full branch history intact
- `Rebase` rewrites your branch commits on top of another branch; creates a linear, cleaner history but rewrites commit hashes
- merging preserves the full true history of all commits, but rebasing is nicer because it makes a linear history of commits on main and is easier to read. Basically gets rid of junk commits no one cares about
- `fork` is not a git feature, it's a feature of github, bitbucket, etc. that just creates a copy of a repo you can modify without affecting the original
- the standard flow to commit to open source is to fork a repo, create a new branch on your local copy of the fork, push the changes to that branch, and open a PR to the original repo. The owners of that repo can then approve your changes
- `git reflog` is your friend for finding lost changes locally

## How git stores things

- `git cat-file` is useful to inspect commits, trees, and blobs
- `tree` is a git directory
- `blob` is a git file

## Reflog & recovery

- `reflog` is like git log but stands for "reference log". Logs changes to a reference over time (like if you switch branches). Keeps track of where HEAD has been
- if you delete a branch or something you can use reflog to find the commit from the history, then use `cat-file` to find the tree w/ the commit hash, and then use that to find the blob of what you lost. This is all possible without ever pushing changes to remote. So I think this means as long as you commit changes and don't nuke your local .git directory there's a way to recover most things
- `git merge` can take a "commitish" which is something that looks like a commit (branch, tag, commit, HEAD@{1}, etc.)
- so instead of the cat-file workflow you could just pass the git merge HEAD@{1} to get the same result

## Merge conflicts

- HEAD (ours) is the current branch you're on. theirs is the branch you're merging into your current one
- merge conflicts happens when multiple branches made changes to the same file without clear lineage
- `checkout` --theirs `path/to/file` allows you to just keep the version of the file you're merging into the current branch. --ours lets you keep your current version
- the point of merge commits is to document what you did during resolving of merge conflicts

## Rebase conflicts

- rebasing rewrites git commit history so it's seen as more risky
- conflicts move you to a "detached head" state where you're not on an official branch until conflicts are resolve. You can use the same conflict tools like checkout during a rebase
- "theirs" and "ours" gets basically flipped during a rebase. So "theirs" are the changes you made on your branch, "ours" is the changes that are on the base you're rebasing onto. Because it takes the point of view of that branch and replays your changes on it commit by commit
- it can rewrite git history because if commits that existed previously aren't necessary after you remove the conflict git will remove them or modify them. You can also change part of a commit and that commit would just be edited
- git `rerere` which you can enable locally remembers how you resolved changes and can use this in the future. This will help with repeated resolutions for things like merging long lived feature branches that will share conflicts
- you don't need to commit in a rebase, you just `--continue`

## Squashing

- rewrites history to combine changes from multiple commits into 1. done by rebasing (a little confusing)
- `git rebase -i HEAD~n` where `n` is the number of commits back you want to squash. change the word from pick to squash for all but the first commit, save and close the editor
- might need to --force-with-lease or --force to update remote

## Stashing

- `git stash` records the current state of your working directory and reverts it to `HEAD`. `git stash list` shows you your stashes
- you can list and pop you stashes (it's a LIFO data structure). Staged and non staged changes can both be stashed (just whatever is not committed)
- you can include a message with your stash but usually you'll have few enough that you don't need that
- `git stash apply` applies the latest stash changes without popping. `drop` removes the most recent changes without applying. You can also reference a stash by index without needing to just work with the top one

## Reverting & reset

- revert is basically an anti commit, it creates a new commit that does the opposite of the changes you did in the commit you're reverting (so you have history of both). Much more precise that `reset`
- You just pass a hash of that commit
- reset commands
    - `soft` undo commits but keeps changes staged
    - `hard` undo commits and discard changes
    - `revert` creates commit that undoes changes
    - usually reset is more for your own branches, revert is for shared branches

## Cherry picking

- cherry picking with `git cherry-pick` just let's you pull in specific commits via their hash to your current branch. basically a more precise merge

## Bisecting

- just does binary search over a list of commits
- bisect process:
    - start bisect
    - select a good commit
    - select a bad commit
    - git will checkout a commit between the good and bad, you can test to see if the bug is present
    - you execute `git bisect` good or bad to say if the current one is good or bad
    - it loops this way until you find the commit that introduced a bug or change you're looking for
- if you have automated testing you can automate bisecting by just running the test on each commit to tell good/bad

## Worktrees

- a worktree is just the directory on your filesystem where the code you're tracking with git lives. It's usually just the root of your git repo that contains tracked, untracked, and modified files
- `linked worktrees` are lighter than a clone or init because it doesn't contain a bunch of data, it just has a .git file with a path to the main working tree. But this can be complicated if you have env files and secrets in the main one
- you can create and switch branches in worktrees
- the big rule is you can't work on a branch that's checked out by any other working tree
- `.git/worktrees` keeps a record of all the worktrees, linking, and mapping
- worktrees are better than stashing for long lived changes
- you can remove with `remove` command. Or if you delete the dir of the worktree you can use `prune`

## Tags

- A git tag is a named link to a commit that doesn't move between commits (unlike branches). They can be deleted or created but are immutable
- used to denote releases in a lot of cases
- commits can have multiple tags
