# macOS shell

Verify hash
---

```
shasum -a ALGORITHM -c FILE_TO_CHECK
```

Example:

```
shasum -a 256 -c myfile.iso.sha256
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


Setup NFS automount
---

Add NFS share mountpoints to fstab using the editor tool _vifs_:

```
sudo vifs
```

Here are some examples:

```
192.168.2.1:/srv/files /System/Volumes/Data/srv/files nfs rw,no_subtree_check,sync,insecure
192.168.2.1:/srv/shared /System/Volumes/Data/srv/shared nfs rw,no_subtree_check,sync,insecure,all_squash,anonuid=1000,anongid=1000
```

Mount shares (initially, mounts automatically after that):

```
sudo automount -cv
```

Groups
===

List groups:
```
dscl . list /groups
```

Add group:
```
sudo dscl . -create /Groups/NEW_GROUP_NAME PrimaryGroupID GROUP_ID
```

Add user to group:
```
sudo dscl . -append /Groups/GROUP_NAME GroupMembership USER_NAME
```

Delete user from group:
```
sudo dscl . -delete /Groups/GROUP_NAME GroupMembership USER_NAME
```

List group members:
```
dscl . -read /Groups/GROUP_NAME
```

Check membership:
```
dseditgroup -o checkmember -m USER_NAME GROUP_NAME
```

Flush dscl cache:
```
sudo dscacheutil -flushcache
```

Finder
===

### Hidden file extensions

Sometimes, file extensions are hidden, especially for screenshots. It's painful to check the mark on every file in Finder by hand. Instead, we can use the terminal!

Show all file extensions:
```
setFile -a e *
```

Hide all file extensions:
```
setFile -a E *
```
