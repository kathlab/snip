# KVM GPU passthrough (NVIDIA)

Blacklist GPU device
---

/etc/modprobe.d/vfio.conf

```
blacklist nvidia
blacklist nvidia-modeset
blacklist nvidia-drm
blacklist nvidia-uvm
blacklist nouveau
blacklist snd-hda-intel
options vfio-pci ids=
```

