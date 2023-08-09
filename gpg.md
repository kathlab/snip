# GnuPG

Show infos from a key file without import
===

```
cat KEYFILE | gpg --no-default-keyring --show-keys --with-fingerprint -
```

Symmetric encryption
===

```
gpg --output FILE.enc --symmetric --no-symkey-cache --cipher-algo AES256 FILE.txt
```

Symmetric decryption
===

```
gpg --output FILE-dec.txt --symmetric --no-symkey-cache --decrypt FILE.txt
```
