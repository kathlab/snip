# macOS shell

Verify hash
---

```
$ shasum -a ALGORITHM -c FILE_TO_CHECK
$ shasum -a 256 -c myfile.iso.sha256
```


Faster mouse movement
---

I experienced using the Magic Mouse on a multi-monitor setup is really exausting even with the highest speed setting. Luckily, there's a way to overdrive the setting beyond the maximum limit:

Get current value:

```
defaults read -g com.apple.mouse.scaling
```

Set value:

```
defaults write -g com.apple.mouse.scaling 5.0
```