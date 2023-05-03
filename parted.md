# Parted snips

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