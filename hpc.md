![alt text](assets/hpc_header.png)

# Welcome to good ol' HPC Cheat-Cheats!

You want more computation Power? Then it's time to get you ready to go!

# 0. Why HPC?

Time and resources. That's why. See, if you have a computational problem that needs excessive amounts of RAM or takes weeks to finish a HPC could be able to solve such issues. However, an HPC is no simple magic. It does it's magic by running a massive amount of parallel jobs or enable you to use a huge amount of resouces like GPU memory. Here's a real world measured example:

Let's say a task of your big job takes 24 minutes in average and you have 518 of those little tasks. On your 4 core notebook it might take over 2 days. The same task is done in under 2 hours wall time on our HPC.

```
518 Tasks ->
Average experiment duration:                   0:24:45
Total experiment duration sequential:          8 days, 21:47:30
Total experiment duration on 4 Core Notebook:  2 days, 5:26:52
Complete Average Time on HPC (108 Jobs/Batch): 1:58:46
```

# 1. Onboarding

![alt text](assets/onboarding.png)

First, you'll need an account right? So i'll guide you right through:

### On-Premise login: A good password

You can't login on the HPC with any password. However there are some nice workstations you might use in the lab. But I demand at least a bit of security here. So what's a **good password**? Short answer: Something you can't easily guess. But how do you know it's good enough? Use pwscore!

```
sudo apt install libpwquality-tools
```

```
read -s -p "Enter Password: " password; echo "$password" | pwscore; unset password
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
openssl passwd -6 -salt
```

Example:

```
openssl passwd -6 -salt u012345
Password:
Verifying - Password:
$6$u012345$VEqab0VnetnqSh9r67ClxrGj2Bl7jiuFmGE6bH6wQmN1/5YrO504nahRK1jSIxex1WjySV1JQz91OePAMNsB11
```

See the long **cryptic looking line at the end**? This is a password hash. I need that to register your on-premise account. You can then login via your password on any lab workstation you like. But don't forget to send a new password hash if you ever chose to change your password!

Also: This **hash** belongs the the password **"test"**. I won't use that hash anythere else than in this example.

### Remote login: Generate a SSH key

```
cd ~/.ssh && ssh-keygen -t ecdsa -b 521
```

Example:

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


# Navigating the HPC

![alt text](assets/navigator.png)

### Cluster information

List all partitions:
```
scontrol show partitions | grep PartitionName
```

Get available resources from a partition:
```
scontrol show partitions universe | grep TRES
```

### Job queue

Show job queue:
```
squeue -l
```

Show PENDING job:
```
squeue -l --states=pending
```

### Get a detailed report of a job

List relevant info of a job:
```
sacct --format=Account,JobID,State,Elapsed,Reason,Start,Submit,QOS -X -j JOB_ID
```

List detailed info of a job:
```
sacct --format=Account,JobID,State,TotalCPU,Elapsed,MaxDiskRead,MaxDiskWrite,Reason,Start,Submit,QOS -X -j JOB_ID
```

List all failed jobs:
```
sacct --format=JobID,JobName,State,ExitCode -X --state=FAILED -j JOB_ID
```

Get the longest duration of a task:
```
sacct --format=Elapsed -j JOB_ID --noheader|sort|tail -n 1
```

# Container Registry

![alt text](assets/container_registry.png)

List all images:
```
curl -X GET https://REGISTRY_HOST:5000/v2/_catalog
```

List all tags of an image:
```
curl -X GET https://REGISTRY_HOST:5000/v2/IMAGE_NAME/tags/list
```