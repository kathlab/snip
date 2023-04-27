# KVM AMD GPU passthrough for Intel CPU System

1. Get GPU device IDs
---

```
$ lspci -nnk | grep Radeon
17:00.0 VGA compatible controller [0300]: Advanced Micro Devices, Inc. [AMD/ATI] Lexa XT [Radeon PRO WX 3100] [1002:6985]
	Subsystem: Advanced Micro Devices, Inc. [AMD/ATI] Lexa XT [Radeon PRO WX 3100] [1002:0b1e]
17:00.1 Audio device [0403]: Advanced Micro Devices, Inc. [AMD/ATI] Baffin HDMI/DP Audio [Radeon RX 550 640SP / RX 560/560X] [1002:aae0]
	Subsystem: Advanced Micro Devices, Inc. [AMD/ATI] Baffin HDMI/DP Audio [Radeon RX 550 640SP / RX 560/560X] [1002:aae0]
```

Important: You need to extract all device IDs of the GPU device. In this example, the GPU and an audio controller has the IDs _1002:6985_ and _1002:aae0_. Both are mandatory!

2. Blacklist GPU device
---

/etc/modprobe.d/vfio.conf

```
blacklist amdgpu
blacklist snd_hda_intel
options vfio-pci ids=1002:6985,1002:aae0
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
$ lspci -nnk| grep Radeon
17:00.0 VGA compatible controller [0300]: Advanced Micro Devices, Inc. [AMD/ATI] Lexa XT [Radeon PRO WX 3100] [1002:6985]
	Subsystem: Advanced Micro Devices, Inc. [AMD/ATI] Lexa XT [Radeon PRO WX 3100] [1002:0b1e]
17:00.1 Audio device [0403]: Advanced Micro Devices, Inc. [AMD/ATI] Baffin HDMI/DP Audio [Radeon RX 550 640SP / RX 560/560X] [1002:aae0]
	Subsystem: Advanced Micro Devices, Inc. [AMD/ATI] Baffin HDMI/DP Audio [Radeon RX 550 640SP / RX 560/560X] [1002:aae0]
```

6. Create Ubuntu VM with passthrough
---

Ensure that the host-device matches 

```
virt-install \
--name ubuntu \
--ram 8192 \
--disk path=/mnt/ssd/VMs/ubuntu/ubuntu.img,size=128,device=disk,bus=virtio \
--vcpus 4 \
--os-variant ubuntu22.04 \
--graphics vnc,listen=0.0.0.0,password=password \
--video vga \
--network bridge=virbr0 \
--console pty,target_type=serial \
--location /mnt/ssd/iso/ubuntu-22.04.2-desktop-amd64.iso,kernel=jammy/vmlinuz,initrd=jammy/initrd \
--extra-args 'console=ttyS0,115200n8' \
--host-device 17:00.0 \
--host-device 17:00.1 \
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