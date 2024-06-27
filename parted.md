# Parted snips

Every command here **DESTROYS** your data on the disk you perform those actions! So please be sure you got the right disk!

### Create XFS partitions without nagging from parted

Parted likes to nag about non performant partitions sizes. I understand, parted likes the partition sector size to be n mod 2048 = 0. Here's how you do it:

```
sudo parted /dev/sda
unit s
print
Model: ATA TOSHIBA DT01ACA2 (scsi)
Disk /dev/sda: 3907029168s
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
```

See the size of the disk in sectors? First we start not directly at sector 0 - parted doesn't like that anyway. To make a partition that fits then in a 2^11 size, we need to calculate the correct sector size:

```
echo "(3907029168 - 2048) % 2048" | bc
176

echo "(3907029168 - 2048 - 176) % 2048" | bc
0

echo "(3907029168 - 2048-176)" | bc
3907026944
```

Perfect, now we partition the disk:

```
mktable gpt
mkpart data xfs 2048 3907026944
quit
```
See? No nag :)

### Create Win11 boot stick

Another slacky example from me trying to create an Win11 usb boot stick manually. Failed with that but got a nice example of doing some partitioning :D

```
(parted) mktable 
New disk label type?                                                      
aix    amiga  atari  bsd    dvh    gpt    loop   mac    msdos  pc98   sun    
New disk label type? gpt
Warning: The existing disk label on /dev/sdc will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? yes                                                               
(parted) print                                                            
Model: SanDisk Cruzer Blade (scsi)
Disk /dev/sdc: 30,8GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start  End  Size  File system  Name  Flags

(parted) mkpart fat32 1M 4G
(parted) mkpart ntfs 4G 12G
(parted) print                                                            
Model: SanDisk Cruzer Blade (scsi)
Disk /dev/sdc: 30,8GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name   Flags
 1      1049kB  4000MB  3999MB               fat32
 2      4000MB  12,0GB  8000MB               ntfs

(parted)
```