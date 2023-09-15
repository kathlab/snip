# HPC Research Cluster

This Snip is all about installing a **HPC cluster** which uses containers in a multi-user environment. This is super highly work-in-progress - don't expect this to be complete or correct. This is a more somewhat structured thinking process. I'm currently working on this! This Snip may drink away all your coffee, so beware!

I'm using Ubuntu 22.04 as a base system. The scheduler is slurm, running unprivileged containers is done by Apptainer (Singularity).


## Local Docker Registry?

Sifs don't need a registry. Just copy the file/sandbox anythere.

Apptainer can't pull from a non-repo as far as I know. To mitigate this, a local docker registry is feasable. That can be installed as a docker container itself.

Workflow thoughts:

* User works with Docker Desktop to create a container -> User pushes to local Docker registry
* Job on compute node -> pulls from local Docker registry via Apptainer
* On compute node, Apptainer converts Docker image to sif and runs container


### Questions

* Single HPC User? Job als User starten, Job läuft als HPC_USER? ❗ probably a don't want (solution: just sync users)
* Slurm Job Pipelines
* Cluster Bildung ✅ works
* Kommunikation zwischen Slurm und Apptainer? ✅ via submit file (sbatch)
* Apptainer-> Volumes, Verzeichnisse reinhängen?
* ITSEC: Sandbox (directory struct.) vs. Sif-File (binary)
* Wie kann man mit slurm die Auslastung von CNs monitoren/betrachten?❓json export to monitoring server?
* Welcher User bin ich unter einer zugewiesenen Slurm Session/Shell?
* Wer baut Sifs?
* (Paralleles) bauen von Sifs auf nicht-CNs ODER bauen in den Job reinnehmen? (Besser in den Job nehmen)
* Wie stellen wir sicher dass der Sif-Bau nicht ständig fehlschlägt?
* Kann ein Container einfach so ins Internet?
* Kann ein Container über eine IP Adresse angesprochen werden?
* Wie kann man meherere Partitionen erzeugen/konfigurieren auf den Nodes?
* Unterschiedliche HW CN Konfiguration?
* Quota flexibel anpassbar für User Verzeichnisse?
* Fast-Storage als GRES für SBATCHes?
* 1 GPU für mehrere Jobs gleichzeitig nutzen? (GRES shared arg?)
* Nvidia GPU im Container ✅ funktioniert
* GPU Job Slurm Pipeline ✅ funktioniert
* Apptainer container bau ✅ funktioniert (Workstations)

### Concepts

* Administration via Ansible
* Storage grundsätzlich von Remote ins Home verlinkt
* Fast-Storage (lokal) nur als Option, muss in Submit Skript z.B. per rsync Befehl gesynct werden
* User Auth per ausgegebenen SSH Key + User-AD-Nummer, den Rest per Ansible
* Gute Default Werte als Container Templates (small, medium, large)
* Auf WebGUI für SBATCHes Häckchen für Fast-Storage, SBATCHes wären read-only

### Ideas

* Es dürfen nur Workloads in Container Images der lokalen Container Registry auf CNs laufen
* Submit Skript Generator als Web-App (Angabe welcher Container gepullt werden soll im Submit Skript): https://www.uni-kassel.de/hrz/db4/scriptgen/
* Slurm LINT (Syntax Check von Submit Skripts)
* E-Mail wenn Job fertig?

### Erkenntnisse

Apptainer def file:

* Alles was in %startscript steht wird nur dann ausgeführt, wenn apptainer instance start genutzt wird. NICHT bei apptainer exec!
* Nvidia enable -> --nv
* Nvidia Cuda installiert im Container funktionert (auch mit älterer Version)
* Sif container Bau braucht viel CPU
* Bottleneck Storage 2 CN

### Todo

-> Anforderungen an das Cluster (was benötigt wird) zusammenfassen
-> Fragenkatalog Liste erstellen
-> Was wir lauffähig getestet haben
-> Nächste Schritte HPCX+HPCY Cluster bauen

## Management Node

* Baut sif Images mit Apptainer und speichern in Ordner mit UniqueID (führt sie nicht aus)
* Jobs auf CNs starten dieses gebaute Sif

## Use cases

### Use case: "Researcher"

A user performs productive computation tasks on HPC cluster. Could be a researcher/student.

1. Non-interactive: Prepare input -> Pull -> Run -> Result
2. Semi-Interactive: Prepare input -> Pull -> Run -> Monitor stats on a Web Interface -> Run -> Result
3. Interactive: Pull -> Run -> Work on Web Interface -> Shutdown (Input/Output on filesystem)

## Use case: "User"

A user uses a service (e.g. a trained model) as a service. Think of an app that can be used by anyone.

1. Semi-Interactive (SAAS): Pull -> Run -> Do something on Web Interface (with up/download of data?) -> Run -> Result

## Sources

* https://slurm.schedmd.com/quickstart_admin.html
* https://github.com/apptainer/apptainer
* https://apptainer.org/docs/user/main/quick_start.html#quick-installation
* https://apptainer.org/docs/user/main/gpu.html
* https://apptainer.org/user-docs/master/singularity_and_docker.html

## Other institutes

* https://howto.cs.uchicago.edu/slurm (very good howto)
* https://scicomp.ethz.ch/wiki/GPU_job_submission_with_SLURM (GPU)
* https://researchcomputing.princeton.edu/support/knowledge-base/slurm#gpus (GPU)
* https://curc.readthedocs.io/en/latest/compute/modules.html (SBATCH modules)
* https://www.sherlock.stanford.edu/docs/software/using/singularity/#pulling-ngc-images
* https://sciwiki.fredhutch.org/compdemos/Apptainer/
* https://guiesbibtic.upf.edu/recerca/hpc/running-singularity-containers
* https://docs.unity.rc.umass.edu/slurm/arrays.html
* https://docs.rc.fas.harvard.edu/kb/singularity-on-the-cluster/

## Multi-Node Computing

PyTorch and TensorFlow:

* https://devblog.pytorchlightning.ai/how-to-configure-a-gpu-cluster-to-scale-with-pytorch-lightning-part-2-cf69273dde7b
* https://deepsense.ai/tensorflow-on-slurm-clusters/

Install Docker and NVIDIA Toolkit
===

Check snip here: https://github.com/kathlab/snip/blob/main/docker_nvidia.md


Install local docker repository
===

A local docker registry is required to get docker images work with Apptainer. Otherwise you are restricted to docker hub images.

```
Not yet done
```


Install Apptainer (non-setuid, non-privileged)
===

The documentation seems not very clear which mode to use, so it's up to the user and his use cases. Both modes (setuid, non-setuid) has clear advantages/disadvantages. This attempt to get a working HPC cluster breaks down to these requirements:

* Multi-user ability. Every container stays within a user's context. User A can't do anything with Containers of user B, not even see them. ✅ works
* No sudo/root required. All tasks must work without any elevated rights. Comes with limitations which are fine (e.g. no Portmapping on privileged network ports allowed). ✅ works
* Root should be able to do things on all containers. ✅ works
* Limit computational resources on a container. ❓ should work, not tested yet ✅ tested with gpu
* GPU within container. ✅ works
* Filesystem mapping. ✅ works
* Long-term archival of containers. Is designed with that in mind ✅ works

https://apptainer.org/docs/admin/main/installation.html

The installation uses an official Ubuntu/ppa:

```
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:apptainer/ppa
sudo apt update
sudo apt install -y apptainer
```

Test:

Should output a version number.

```
apptainer exec docker://alpine cat /etc/alpine-release
```


### Pull docker container from registry

To pull images from docker, the url must be used.

```
# pull image + convert to sif
apptainer pull docker://docker.io/nginx:mainline-alpine3.18

# show running containers
apptainer instance list

# start container sif-file, using name "web" for the container
# nginx requires a writable container (sif-file via overly tmpfs)
apptainer instance start --writable-tmpfs nginx.sif web

# stop container instance
apptainer instance stop web
```

These commands didn't work well:

```
apptainer instance start --mount type=bind,source=./apptainer/nginx/,dst=/usr/share/nginx/html,ro ./nginx_latest.sif nginx_test
apptainer instance start --mount type=bind,source=./apptainer/nginx/,dst=/usr/share/nginx/html,ro ./nginx_latest.sif nginx_test
apptainer instance start --mount type=bind,source=/home/USER/apptainer/

# privileged port issue when running without sudo. why?
nginx/,dst=/usr/share/nginx/html,ro --net --network-args "portmap=8080:80/tcp" ./nginx_mainline-alpine3.18.sif nginx_test
```


## Build Apptainer sif image

```
Bootstrap: docker
From: nginx

%startscript
   nginx
```

## Build custom sif image

```
Bootstrap: docker
From: ubuntu

%files
    ./upper.sh /opt/

%startscript
   /opt/upper.sh ${INPUT}
   
%environment
   INPUT=${INPUT_FILE}
```

```
apptainer build upcase.sif upcase.def
apptainer instance start --env "INPUT=/home/USER/apptainer/upcase/test.txt" upcase.sif upcase

apptainer shell instance://upcase
apptainer instance stop upcase
```


```
# build container
apptainer build upcase_alt.sif upcase_alt.def

# start container
apptainer instance start upcase_alt.sif upcase_alt

# run app in container
apptainer exec instance://upcase_alt /opt/upper.sh test_alt.txt

# stop
apptainer instance stop upcase_alt
```

### Create slurm job submit script


Learnings:

* Don't get confused with local/container paths
* Add path binds if apptainer runs outside workingdir
* Everything runs as the user which started the job
* SSH-Keys must exchanged in advance

```
#!/bin/bash

#SBATCH --job-name="upcase_job"
#SBATCH --partition=debug
#SBATCH --output=/media/storage/upcase_job.out
#SBATCH --error=/media/storage/upcase_job.err
#SBATCH --nodes=1
#SBATCH -c 1
#SBATCH --mem=4096

# copy sif to CN
scp mini-hpc:~/apptainer/upcase/upcase.sif /media/storage/

# copy input data to CN
scp mini-hpc:~/apptainer/upcase/test.txt /media/storage/

# run container
# without bind starting apptainer within the workingdir is mandatory!
apptainer exec --bind /media/storage:/mnt /media/storage/upcase.sif /opt/upper.sh /mnt/test.txt

# copy back result to MN
scp /media/storage/test.txt_* mini-hpc:~/apptainer/upcase/result.txt

# cleanup
rm /media/storage/*.*
```

### Sandbox mode (for development, very useful):

Instead of a sif-file you have your container "image" as a subdirectory structure you can run as a container. You can work inside that subdir and make modifications. Very useful when hacking inside/on a container image.

```
# build container sandbox from definition file
apptainer build --sandbox nginx.sif nginx.def

# run container sandbox in write mode (NOT tmpfs mode!)
apptainer instance start --writable nginx.sif/ web # run sandbox in write mode

# stop container sandbox
apptainer instance stop web
```

### Convert sandbox to sif image

Make a sandbox image production-ready. After conversion, the container image is write protected. This is what you want to deploy on a cluster.

```
apptainer build nginx-prod.sif nginx.sif/
apptainer instance start --writable-tmpfs nginx-prod.sif web
apptainer instance stop web
```

## Become root in sif

```
apptainer shell --fakeroot nginx.sif
apptainer shell --writable-tmpfs --fakeroot nginx.sif
apptainer shell --writable --fakeroot nginx.sif # does not work
```


## Become user in running container

```
apptainer shell instance://web
apptainer shell --writable-tmpfs instance://web
```


## Admin list container from user (as root)

```
apptainer instance list -u USER
```


# Install slurm

Cluster setup:

* Compute Node (CN): 10.42.0.24/24
* Management Node (MN): 10.42.0.28/24

## Install Munge (slurm node authentication)

```
sudo apt install munge
```

Copy munge.key from Management Node (MN) to Compute Nodes (CNs)

### On MN:

```
base64 munge.key
<copy-n-paste-key>
md5sum munge.key
```

### On CN:

```
echo -n "BASE64ENCODEDKEY" | base64 -d > munge.key
chown munge:munge munge.key
chmod 0600 munge.key
```

### Check if key matches

```
md5sum munge.key
systemctl restart munge.service
```

## Install slurm on MN

```
sudo apt install slurmctld

# check that user slurm exists
grep slurm /etc/passwd
```

## Install slurm on CN

```
sudo apt install slurmd hwloc
```

### Create Slurm Configs

Slurm Cluster Configurator: https://slurm.schedmd.com/configurator.html


Add machines in /etc/hosts. They need to know each other if hostnames are used. Not necessary if a host pulls it's network configuration from a server.

```
10.42.0.24  mini-hpc
10.42.0.28  cn-01

# check with ping for connectivity
ping mini-hpc
ping cn-01
```

### Edit /etc/slurm/cgroup.conf

https://slurm.schedmd.com/cgroup.conf.html

```
CgroupAutomount=yes
ConstrainCores=yes
ConstrainDevices=yes
ConstrainRAMSpace=yes
ConstrainSwapSpace=yes
```

### Disable cgroupv2 (only support with newer slurm)

* Older slurm does not like cgroupv2 hybrid
* Newer slurm version required!

```
vi /etc/defaults/grub

# Add to kernel boot args
systemd.unified_cgroup_hierarchy=0
```

### Create /var/spool/slurmctld on MN:

```
mkdir /var/spool/slurmctld
chown slurm:root /var/spool/slurmctld
```

### Create slurm config

```
# slurm.conf file generated by configurator.html.
# Put this file on all nodes of your cluster.
# See the slurm.conf man page for more information.
#
ClusterName=cluster01
SlurmctldHost=mini-hpc
#SlurmctldHost=
#
#DisableRootJobs=NO
#EnforcePartLimits=NO
#Epilog=
#EpilogSlurmctld=
#FirstJobId=1
#MaxJobId=67043328
#GresTypes=
#GroupUpdateForce=0
#GroupUpdateTime=600
#JobFileAppend=0
#JobRequeue=1
#JobSubmitPlugins=lua
#KillOnBadExit=0
#LaunchType=launch/slurm
#Licenses=foo*4,bar
#MailProg=/bin/mail
#MaxJobCount=10000
#MaxStepCount=40000
#MaxTasksPerNode=512
MpiDefault=none
#MpiParams=ports=#-#
#PluginDir=
#PlugStackConfig=
#PrivateData=jobs
ProctrackType=proctrack/cgroup
#Prolog=
#PrologFlags=
#PrologSlurmctld=
#PropagatePrioProcess=0
#PropagateResourceLimits=
#PropagateResourceLimitsExcept=
#RebootProgram=
ReturnToService=1
SlurmctldPidFile=/var/run/slurmctld.pid
SlurmctldPort=6817
SlurmdPidFile=/var/run/slurmd.pid
SlurmdPort=6818
SlurmdSpoolDir=/var/spool/slurmd
SlurmUser=slurm
#SlurmdUser=root
#SrunEpilog=
#SrunProlog=
StateSaveLocation=/var/spool/slurmctld
SwitchType=switch/none
#TaskEpilog=
TaskPlugin=task/affinity,task/cgroup
#TaskProlog=
#TopologyPlugin=topology/tree
#TmpFS=/tmp
#TrackWCKey=no
#TreeWidth=
#UnkillableStepProgram=
#UsePAM=0
#
#
# TIMERS
#BatchStartTimeout=10
#CompleteWait=0
#EpilogMsgTime=2000
#GetEnvTimeout=2
#HealthCheckInterval=0
#HealthCheckProgram=
InactiveLimit=0
KillWait=30
#MessageTimeout=10
#ResvOverRun=0
MinJobAge=300
#OverTimeLimit=0
SlurmctldTimeout=120
SlurmdTimeout=300
#UnkillableStepTimeout=60
#VSizeFactor=0
Waittime=0
#
#
# SCHEDULING
#DefMemPerCPU=0
#MaxMemPerCPU=0
#SchedulerTimeSlice=30
SchedulerType=sched/backfill
SelectType=select/cons_tres
#
#
# JOB PRIORITY
#PriorityFlags=
#PriorityType=priority/basic
#PriorityDecayHalfLife=
#PriorityCalcPeriod=
#PriorityFavorSmall=
#PriorityMaxAge=
#PriorityUsageResetPeriod=
#PriorityWeightAge=
#PriorityWeightFairshare=
#PriorityWeightJobSize=
#PriorityWeightPartition=
#PriorityWeightQOS=
#
#
# LOGGING AND ACCOUNTING
#AccountingStorageEnforce=0
#AccountingStorageHost=
#AccountingStoragePass=
#AccountingStoragePort=
AccountingStorageType=accounting_storage/none
#AccountingStorageUser=
#AccountingStoreFlags=
#JobCompHost=
#JobCompLoc=
#JobCompParams=
#JobCompPass=
#JobCompPort=
JobCompType=jobcomp/none
#JobCompUser=
#JobContainerType=job_container/none
JobAcctGatherFrequency=30
JobAcctGatherType=jobacct_gather/none
SlurmctldDebug=info
SlurmctldLogFile=/var/log/slurmctld.log
SlurmdDebug=info
SlurmdLogFile=/var/log/slurmd.log
#SlurmSchedLogFile=
#SlurmSchedLogLevel=
#DebugFlags=
#
#
# POWER SAVE SUPPORT FOR IDLE NODES (optional)
#SuspendProgram=
#ResumeProgram=
#SuspendTimeout=
#ResumeTimeout=
#ResumeRate=
#SuspendExcNodes=
#SuspendExcParts=
#SuspendRate=
#SuspendTime=
#
#
# COMPUTE NODES
NodeName=cn-01 CPUs=1 State=UNKNOWN
PartitionName=debug Nodes=ALL Default=YES MaxTime=INFINITE State=UP
```

## Adding a new hpc user

UIDs and GIDs must match on all nodes!

```
sudo useradd -m -s /bin/bash -u 1100 -U -G adm,sudo,docker hpcuser
sudo passwd hpcuser
```

## Run container as slurm job

```
# cluster check
sinfo

# submit job
sbatch job.sbatch

# check jobs
squeue -u USERNAME

# delete job
scancel JOB_ID
```

## Undrain CN

Sometimes a job just keeps hanging a node leaving it in a "drained" state. Here's how to solve that:

```
# show node state
scontrol show node cn-01

# set node state to down
scontrol update NodeName=cn-01 State=DOWN Reason="undraining"

# set node state to resume
scontrol update NodeName=cn-01 State=RESUME
```

# GPU resources

## show node info gres

```
scontrol show node cn-01

# daemon reload config
# does not work if gres.conf has changed! fix: restart systemd service
scontrol reconfigure
```

## gres.conf on a CN

Some terminology:

* COREs (Number of cores on a CPU single socket)
* Compute/CUDA Multi-Process Service (MPS)

```
##################################################################
# Slurm's Generic Resource (GRES) configuration file
# Define GPU devices with MPS support, with AutoDetect sanity checking
##################################################################
# AutoDetect=nvml # does not work without nvidia nvml compiled in slurm!
Name=gpu Type=rtx3070 File=/dev/nvidia0 COREs=[0-3]
# Name=mps Count=100 File=/dev/nvidia0 COREs=[0-3] # probably a don't need
# Name=bandwidth Count=1G # only for lustery fs NOT ethernet!
```

## slurm.conf

```
GresTypes=gpu # ,mps,bandwidth
NodeName=cn-01 CPUs=1 State=UNKNOWN Gres=gpu:rtx3070:1
```

## Getting gres infos

```
# shows cores count, etc.
lstopo -l

# shows nvidia card
nvidia-smi -L

# shows more specific info
nvidia-smi --query-gpu gpu_name,driver_version,compute_mode,compute_cap --format=csv

# list nvidia devices
ls /dev/nvidia*
```

### Example GPU container image def

```
Bootstrap: docker
From: nvcr.io/nvidia/pytorch:23.08-py3

%files
   ./check.py /opt/

%startscript
   nvidia-smi
```

### Example GPU SBATCH

```
#!/bin/bash

#SBATCH --job-name="ngc_job"
#SBATCH --partition=debug
#SBATCH --output=/media/storage/ngc_job.out
#SBATCH --error=/media/storage/ngc_job.err
#SBATCH --nodes=1
#SBATCH -c 1 # Max amount of cores
#SBATCH --mem=4096
#SBATCH --gres=gpu:1 # use 1 GPU

CNAME="ngc"

echo "copy sif"
scp mini-hpc:~/apptainer/$CNAME/$CNAME.sif /media/storage/

echo "run container"
apptainer exec --nv --bind /media/storage:/mnt /media/storage/$CNAME.sif python /opt/check.py

# cleanup
rm /media/storage/*.*

echo "done"
```

### Check pytorch

check.py outputs CPU if something doesn't work!

```
import torch

if torch.cuda.is_available():
    print("TORCH CUDA device name: " + torch.cuda.get_device_name(0))
    print("TORCH CUDA device count: " + str(torch.cuda.device_count()))
else:
    print("TORCH device name: " + "[CPU]")
```

# Anything beyond this point 
Here be dragons 🐉

# Possible cgroupv1 issue

Using a newer slurm version solves the cgroupv2 issue but it may introduce a new issue with cgroupv1. Here's how to deal with that:

Source: https://gist.github.com/pinkeen/bba0a6790fec96d6c8de84bd824ad933

It's not enough that your kernel has cgroupv2 enabled. Depending on the linux distribution bundled systemd might prefer to use v1 by default.

You can tell systemd to use cgroupv2 via kernel cmdline parameter:
systemd.unified_cgroup_hierarchy=1

It might also be needed to explictly disable hybrid cgroupv1 support to avoid problems using: systemd.legacy_systemd_cgroup_controller=0

Or completely disable cgroupv1 in the kernel with: cgroup_no_v1=all

### fix for nvml?

Source: https://www.mail-archive.com/slurm-users@lists.schedmd.com/msg07024.html

```
Under Debian Stretch/Buster I had to set LDFLAGS=-L/usr/lib/x86_64-linux-gnu/nvidia/current for configure to find the NVML shared library. 
```

### Set account for SBATCH

```
#SBATCH --account=owner-guest
```