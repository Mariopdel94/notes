#git 

Git alias command to get the main branch name:

```
.gitconfig
[alias]
	main-branch = !git symbolic-ref refs/remotes/origin/HEAD | cut -d'/' -f4
```