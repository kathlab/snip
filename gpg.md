# GnuPG

Cool snips to work with gpg.

Add keyserver
===

```
echo "keyserver hkps://keys.openpgp.org" > ~/.gnupg/gpg.conf
```

Search a key by fingerprint
===

```
gpg --search-keys FINGERPRINT
```

Refresh keys
===

```
gpg --refresh-keys
```

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
