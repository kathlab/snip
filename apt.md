# apt daily's

Get updates
---

```
$ sudo apt update
$ sudo apt upgrade
```

Perform _held-back_ upgrades:
---

```
$ apt list --upgradable
$ apt install PACKAGE_FROM_LIST
```

Repeat until done. Don't use apt dist-upgrade for that!