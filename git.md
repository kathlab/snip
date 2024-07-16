# Git Everyday's

Setup git user and email
---

```
vi ~/.gitconfig 
[user]
        name = Your Name
        email = Your email address
```

Rename master to main
---

```
git branch -m master main
```

Connect existing code to github repository
---

```
git init
git remote add origin URL
```

Change exisiting remote url
---

```
git remote set-url origin URL
git push --set-upstream origin main
```

Add empty folders
---

By default, you cannot add empty folders. But by adding a file __.gitkeep__ within a folder can resolve that issue:

```
touch PATH/.gitkeep
git add PATH/.gitkeep
```

List all files that are inside a git repository
---

```
git log --name-only -n 1 HASH
```

Create a new branch
---

```
git checkout -b my_branch
```

Merge branch with main branch
---

```
git checkout main
git merge my_branch
```

Convert git repository to a bare one
---

This is useful if you like to make a git repository on a remote storage instead of a real git server. Just copy your repository on the destination. Then you can add or change your repository origin to the new path.

```
cp -r ./my_repo/.git /my/awesome/storage/my_repo.git
```

Change core option in __my_repo.git/config__:

```
[core]
        bare = true
```

Undo last commit
---

```
git reset HEAD~
```
