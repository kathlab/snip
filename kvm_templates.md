# KVM virt-install templates

Windows 10 UEFI without secure boot
---

```
virt-install \
--name packager \
--ram 8192 \
--disk path=/VMs/packager/win10.img,size=256,format=qcow2,sparse=true,bus=sata \
--cpu host-passthrough \
--vcpus 4,sockets=1,cores=4,threads=1 \
--os-variant=win10 \
--graphics vnc,listen=0.0.0.0,password=5ZgttR28 \
--video vga \
--console pty,target_type=serial \
--cdrom /VMs/iso/Win10_22H2_German_x64.iso \
--disk /VMs/iso/virtio-win-0.1.229.iso,device=cdrom,bus=sata \
--network bridge=virbr0 \
--host-device 01:00.0 \
--host-device 01:00.1 \
--features kvm_hidden=on \
--tpm type=emulator,version=2.0,model=tpm-tis \
--machine q35
--boot loader=/usr/share/OVMF/OVMF_CODE_4M.fd,loader.readonly=yes,loader.type=pflash,loader.secure=no,nvram.template=/usr/share/OVMF/OVMF_VARS_4M.fd,menu=on
```

Windows 10 Bios mod
---

```
virt-install \
--name packager \
--ram 8192 \
--disk path=/VMs/packager/win10.img,size=256,format=qcow2,sparse=true,bus=sata \
--cpu host-passthrough \
--vcpus 4,sockets=1,cores=4,threads=1 \
--os-variant=win10 \
--graphics vnc,listen=0.0.0.0,password=5ZgttR28 \
--video vga \
--console pty,target_type=serial \
--cdrom /VMs/iso/Win10_22H2_German_x64.iso \
--disk /VMs/iso/virtio-win-0.1.229.iso,device=cdrom,bus=sata \
--network bridge=virbr0 \
--host-device 01:00.0 \
--host-device 01:00.1 \
--features kvm_hidden=on \
--tpm type=emulator,version=2.0,model=tpm-tis \
--machine q35
```

Ubuntu 22.10
---

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

Ubuntu 22.10 with GPU Passthrough
---

```
virt-install \
--name ubuntu \
--ram 8192 \
--disk path=/VMs/ubuntu/ubuntu.img,size=256,format=qcow2,sparse=true,bus=sata \
--cpu host-passthrough \
--vcpus 4,sockets=1,cores=4,threads=1 \
--os-variant=ubuntu:ubuntu22.10 \
--graphics vnc,listen=0.0.0.0,password=5ZgttR28 \
--video vga \
--console pty,target_type=serial \
--cdrom /VMs/iso/ubuntu-22.10-desktop-amd64.iso \
--network bridge=virbr0 \
--host-device 01:00.0 \
--host-device 01:00.1 \
--features kvm_hidden=on \
--tpm type=emulator,version=2.0,model=tpm-tis \
--machine q35
```