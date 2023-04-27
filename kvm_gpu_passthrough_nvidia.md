# KVM NVIDIA GPU passthrough for Intel CPU System

1. Get GPU device IDs
---

```
$ lspci -nnk | grep -i nvidia
01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA104 [GeForce RTX 3070 Lite Hash Rate] [10de:2488] (rev a1)
	Kernel modules: nvidiafb, nouveau, nvidia_drm, nvidia
01:00.1 Audio device [0403]: NVIDIA Corporation GA104 High Definition Audio Controller [10de:228b] (rev a1)
```

Important: You need to extract all device IDs of the GPU device. In this example, the GPU and an audio controller has the IDs _10de:2488_ and _10de:228b_. Both are mandatory!

2. Blacklist GPU device
---

/etc/modprobe.d/vfio.conf

```
blacklist nvidia
blacklist nvidia-modeset
blacklist nvidia-drm
blacklist nvidia-uvm
blacklist nouveau
blacklist snd-hda-intel
options vfio-pci ids=10de:2488,10de:228b
```

3. Configure kernel parameters
---

/etc/default/grub

Modify this line:

```
GRUB_CMDLINE_LINUX="intel_iommu=on iommu=pt kvm.ignore_msrs=1 initcall_blacklist=sysfb_init"
```

4. Update initram and grub
---

```
$ sudo update-initramfs -u
$ sudo update-grub
$ reboot
```

5. Verify passthrough
---

IOMMU must be on, VFIO must be active!

```
sudo dmesg | grep -i -e DMAR -e IOMMU -e VFIO
```

Check that no __Kernel driver in use:__ lines appear in the list of pci devices (Kernel modules lines are ok):

```
$ lspci -nnk
01:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA104 [GeForce RTX 3070 Lite Hash Rate] [10de:2488] (rev a1)
	Subsystem: Dell GA104 [GeForce RTX 3070 Lite Hash Rate] [1028:c903]
	Kernel modules: nvidiafb, nouveau, nvidia_drm, nvidia
01:00.1 Audio device [0403]: NVIDIA Corporation GA104 High Definition Audio Controller [10de:228b] (rev a1)
	Subsystem: Dell GA104 High Definition Audio Controller [1028:c903]
	Kernel modules: snd_hda_intel
```

6. Create Ubuntu VM with passthrough
---

Ensure that the host-device matches 

```
virt-install \
--name matrix \
--ram 8192 \
--disk path=/VMs/matrix/matrix.img,size=128,device=disk,bus=virtio \
--vcpus 4 \
--os-variant ubuntu22.04 \
--graphics vnc,listen=0.0.0.0,password=password \
--video vga \
--network bridge=virbr0 \
--console pty,target_type=serial \
--location /VMs/iso/ubuntu-22.10-desktop-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd \
--extra-args 'console=ttyS0,115200n8' \
--host-device 01:00.0 \
--host-device 01:00.1 \
--features kvm_hidden=on \
--machine q3
```

7. Get graphical console
---

If necessary, start SSH tunnel:

```
$ ssh -L 5900:127.0.0.1:5900 vm_host
```

Then connect via VNC. Don't forget to start the VM.