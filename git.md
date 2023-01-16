# Git Everyday's

Rename master to main
---

```
$ git branch -m master main
```

Connect existing code to github repository
---

```
$ git init
$ git remote add origin URL
```

Add empty folders
---

By default, you cannot add empty folders. But by adding a file __.gitkeep__ within a folder can resolve that issue:

```
$ touch PATH/.gitkeep
$ git add PATH/.gitkeep
```

List all files that are inside a git repository
---

```
git log --name-only -n 1 HASH
```