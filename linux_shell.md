# Linux shell

Merge PDFs
---

Add alias to bashrc:

```
alias mergepdf='gs -dNOPAUSE -sDEVICE=pdfwrite -sOUTPUTFILE=~/Documents/`date +%F`_merged.pdf -dBATCH '
```

Start a terminal and type __mergepdf__ and drag-and-drop PDF files in the order you want to have them merged. Then press __enter__.

Use sudo and tee
---

Useful when writing requires elevated access rights.

```
echo HELLO | sudo tee OUTPUT
```

Chroot
---

Very useful if you have to get into an linux system that is on disk somewhere. Just boot from USB, mount the drive and mount the os drive in /mnt. Also, ```lsblk``` is a very nice program to show you all disk devices in the system.

```
mount /dev/DISK_THERE_THE_OS_IS_ON /mnt
mount --bind /dev /mnt/dev 
mount devpts -t devpts /mnt/dev/pts
mount proc -t proc /mnt/proc
mount sysfs -t sysfs /mnt/sys
```