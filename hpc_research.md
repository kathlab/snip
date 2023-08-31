# HPC Research Cluster

This Snip is all about installing a **HPC cluster** which uses containers in a multi-user environment. This is super highly work-in-progress - don't expect this to be complete or correct. This is a more somewhat structured thinking process. I'm currently working on this! This Snip may drink away all your coffee, so beware!

I'm using Ubuntu 22.04 as a base system. The scheduler is slurm, running unprivileged containers is done by Apptainer (Singularity).


## Local Docker Registry?

Apptainer can't pull from a non-repo as far as I know. To mitigate this, a local docker registry is feasable. That can be installed as a docker container itself.

Workflow thoughts:

User works with Docker Desktop to create a container
-> User pushes to local Docker registry

Job on compute node
-> pulls from local Docker registry via Apptainer

On compute node, Apptainer converts Docker image to sif and runs container

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

* https://www.sherlock.stanford.edu/docs/software/using/singularity/#pulling-ngc-images
* https://sciwiki.fredhutch.org/compdemos/Apptainer/
* https://guiesbibtic.upf.edu/recerca/hpc/running-singularity-containers
* https://docs.unity.rc.umass.edu/slurm/arrays.html
* https://docs.rc.fas.harvard.edu/kb/singularity-on-the-cluster/

# Offene Fragen

* Multi User? Job als User starten, Job läuft als HPC_USER?
* Slurm Job Pipelines
* Cluster Bildung
* Kommunikation zwischen Slurm und Apptainer?
* Apptainer-> Volumes, Verzeichnisse reinhängen?
* ITSEC: Sandbox (directory struct.) vs. Sif-File (binary)


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
* Limit computational resources on a container. ❓ should work, not tested yet
* GPU within container. ❓ should work, not tested yet
* Filesystem mapping. ❓ should work, not tested yet

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


### Sandbox mode (for development, very useful ):

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




# Anything beyond this point 
Here be dragons 🐉

# Install slurm

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