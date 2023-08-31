# Hacking / IT-Sec

Naa, not the bad stuff! Just some useful Snips around bits and bytes.

## Checkout data

```
hexdump -C FILE
```

## Secure erase

Also works with hardware devices like HDDs, SSDs.

```
shred -vzn 7 FILE
```