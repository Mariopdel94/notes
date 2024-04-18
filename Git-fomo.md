#git

A nice trick to automatically merge the main branch into a feature branch is by this `git alias`

```
.gitconfig
[alias]
	fomo = !git fetch origin main && git merge origin/main
```

>**Note**: If you need to `rebase` instead you can replace it for `merge`.

## Improvements:

[[Git - Get main branch name]]

By having an alias to get the main branch name you can change the fomo command to:

```
[alias]
	fomo = !git fetch origin $(git main-branch) && git merge origin/$(git main-branch) --autostash
```

By having `--autostash` it will also stash your current changes (if any) and apply the stash changes after merging