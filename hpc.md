![alt text](assets/hpc_mascot.png)

# Welcome to HPC Cheat-Cheats!

You want more computation Power? Then it's time to release the big kraken on your research projects!

## 1. Onboarding

First, you'll need an account right? So i'll guide you right through:

### On-Premise login: A good password

You can't login on the HPC with any password. However there are some nice workstations you might use in the lab. But I demand at least a bit of security here. So what's a **good password**? Short answer: Something you can't easily guess. But how do you know it's good enough? Use pwscore!

```
sudo apt install libpwquality-tools
```

So, lets look at an example:

```
read -s -p "Enter Password: " password; echo "$password" | pwscore; unset password
supersecure
43
```

See the **second line**? This was my super-secret password I typed in. The password 'supersecure' gets a score of **43**, right? Bad! The best score is a **100**! So there's some room for improvement:

```
read -s -p "Enter Password: " password; echo "$password" | pwscore; unset password
!That%is_alotBetter
100
```

See? That's a perfect **100**! You don't need to go too cryptic but a **score >= 90 is mandatory** for getting access to the lab.

### On-Premise login: Generate a password hash

For any **on-premise login** at a workstation in the lab, a password is mandatory. But you don't want to send me your real password. But instead, just send your password hash:

```
openssl passwd -6 -salt USERNAME
Password: 
Verifying - Password: 
$6$u012345$VEqab0VnetnqSh9r67ClxrGj2Bl7jiuFmGE6bH6wQmN1/5YrO504nahRK1jSIxex1WjySV1JQz91OePAMNsB11
```

See the long **cryptic looking line at the end**? This is a password hash. I need that to register your on-premise account. You can then login via your password on any lab workstation you like. But don't forget to send a new password hash if you ever chose to change your password!

Also: This **hash** belongs the the password **"test"**. I won't use that hash anythere else than in this example.

### Remote login: Generate a SSH key



```
cd ~/.ssh && ssh-keygen -t ecdsa -b 521
Generating public/private ecdsa key pair.
Enter file in which to save the key (/home/u012345/.ssh/id_ecdsa): u012345_hpc 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in u012345_hpc
Your public key has been saved in u012345_hpc.pub
The key fingerprint is:
SHA256:qA980ZBoL0e/Q/X2UBeMZKlqw+GKYkYO4ZjYxnW88Oo u012345@myhost
The key's randomart image is:
+---[ECDSA 521]---+
|            .o+. |
|     . .    .o ..|
|    o.+   . . . .|
| . .ooo= o o . . |
|++...+=.S o +    |
|+o+o +oo B . o   |
| .+ +.o = .   .  |
|   =.= . .       |
|  o.E .          |
+----[SHA256]-----+
```


## Navigate the HPC

### List all partitions

```
scontrol show partitions | grep PartitionName
```

### Get available resources from a partition

```
scontrol show partitions universe | grep TRES
```

### Get a detailed report of a job

```
sacct -j JOB_ID --format=Account,JobID,State,Elapsed,Partition,Node,MaxDiskRead,MaxDiskWrite,QOS
```