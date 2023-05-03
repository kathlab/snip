# Virt-install

Some slacky examples.

## Windows 10 with GPU passthrough (non UFI to enable snaphots)

```
#!/bin/bash

virt-install \
--name packager \
--ram 8192 \
--disk path=/VMs/packager/win10.img,size=256,format=qcow2,sparse=true,bus=sata \
--cpu host-passthrough \
--vcpus 4,sockets=1,cores=4,threads=1 \
--os-variant=win10 \
--graphics vnc,listen=0.0.0.0,password=SECRET \
--video vga \
--console pty,target_type=serial \
--cdrom /VMs/iso/Win10_22H2_German_x64.iso \
--disk /VMs/iso/virtio-win-0.1.229.iso,device=cdrom,bus=sata \
--network bridge=virbr0 model=virtio \
--host-device 01:00.0 \
--host-device 01:00.1 \
--features kvm_hidden=on \
--tpm type=emulator,version=2.0,model=tpm-tis \
--machine q35
#--boot loader=/usr/share/OVMF/OVMF_CODE_4M.fd,loader.readonly=yes,loader.type=pflash,loader.secure=no,nvram.template=/usr/share/OVMF/OVMF_VARS_4M.fd,menu=on
```

## Ubuntu 22.04 LTS with GPU passthrough

```
virt-install \
--name ubuntu \
--ram 8192 \
--disk path=/mnt/ssd/VMs/ubuntu_lts/ubuntu.img,size=128,device=disk,bus=virtio \
--vcpus 4 \
--os-variant ubuntu22.04 \
--graphics vnc,port=45910,listen=0.0.0.0,password=SECRET \
--video vga \
--network network=default \
--network bridge=br0 \
--console pty,target_type=serial \
--location /mnt/ssd/iso/ubuntu-22.04.2-desktop-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd \
--extra-args 'console=ttyS0,115200n8' \
--features kvm_hidden=on \
--boot bootmenu.enable=on,bios.useserial=on \
--host-device 17:00.0 \
--host-device 17:00.1
```