# GnuPG

Show infos from a key file without import
===

```
cat KEYFILE | gpg --no-default-keyring --show-keys --with-fingerprint -
```

Symmetric encryption
===

```
gpg --output ENCRYPTED --symmetric --no-symkey-cache --cipher-algo AES256 PLAINTEXT
```

Symmetric decryption
===

```
gpg --no-symkey-cache --decrypt ENCRYPTED --output PLAINTEXT
```
