# HPC Research Cluster

This snip is all about installing a container scheduling system for using on a HPC research cluster. This is highly work-in-progress - don't expect this to be complete. May drink away all your coffee.

I'm using Ubuntu 22.04 as a base system. The scheduler is slurm, running unprivileged containers is done by Singularity.

Install Docker and NVIDIA Toolkit
===

Check snip here: https://github.com/kathlab/snip/blob/main/docker_nvidia.md

Install go language
===

Singularity is written in go. So install that first.

```
sudo apt install golang
```

Install Singularity
===

1. Install necessary packages first

```
sudo apt update && sudo apt install -y build-essential uuid-dev libgpgme-dev squashfs-tools libseccomp-dev wget pkg-config git cryptsetup-bin
```

2. Get deb package from github

Check, which release version is available: https://github.com/sylabs/singularity/releases

```
cd /tmp # necessary to avoid sandboxing issues
wget https://github.com/sylabs/singularity/releases/download/v3.11.3/singularity-ce_3.11.3-jammy_amd64.deb
wget https://github.com/sylabs/singularity/releases/download/v3.11.3/sha256sums
```

check checksum:

```
sha256sum --ignore-missing -c sha256sums
```

3. Install Singularity

```
sudo apt install ./singularity-ce_3.11.3-jammy_amd64.deb
```

Check if everything works:

```
singularity --version
```


Install slurm
===

```
sudo apt install slurmd slurmdbd slurmctld
```

Add /etc/slurm/slurm.conf configuration. Requires modification!

```
# slurm.conf file generated by configurator.html.
# Put this file on all nodes of your cluster.
# See the slurm.conf man page for more information.
#
ClusterName=CLUSTERNAME
SlurmctldHost=localhost
MpiDefault=none
ProctrackType=proctrack/linuxproc
ReturnToService=2
SlurmctldPidFile=/var/run/slurmctld.pid
SlurmctldPort=6817
SlurmdPidFile=/var/run/slurmd.pid
SlurmdPort=6818
SlurmdSpoolDir=/var/lib/slurm/slurmd
SlurmUser=slurm
StateSaveLocation=/var/lib/slurm/slurmctld
SwitchType=switch/none
TaskPlugin=task/none
#
# TIMERS
InactiveLimit=0
KillWait=30
MinJobAge=300
SlurmctldTimeout=120
SlurmdTimeout=300
Waittime=0
# SCHEDULING
SchedulerType=sched/backfill
SelectType=select/cons_tres
SelectTypeParameters=CR_Core
#
#AccountingStoragePort=
AccountingStorageType=accounting_storage/none
JobCompType=jobcomp/none
JobAcctGatherFrequency=30
JobAcctGatherType=jobacct_gather/none
SlurmctldDebug=info
SlurmctldLogFile=/var/log/slurm/slurmctld.log
SlurmdDebug=info
SlurmdLogFile=/var/log/slurm/slurmd.log
#
# COMPUTE NODES
NodeName=localhost CPUs=1 RealMemory=500 State=UNKNOWN
PartitionName=LocalQ Nodes=ALL Default=YES MaxTime=INFINITE State=UP
```