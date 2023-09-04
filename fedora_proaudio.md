# Realtime Pro Audio on Fedora / Pipewire / Ardour

Install realtime packages
===

```
sudo dnf install tuned-profiles-realtime realtime-setup realtime-tests rtirq rtkit kernel-tools
```

Add user to realtime group
===

You have to restart the computer after adding to the group.

```
sudo usermod -a -G realtime USER
```

Install Ardour and audio plugins
===

```
sudo dnf install lv2-abGate.x86_64 lv2-drumkv1.x86_64 lv2-x42-plugins.x86_64 zynaddsubfx-lv2.x86_64 lv2-ll-plugins.x86_64 lv2-calf-plugins.x86_64 lv2dynparam.x86_64 lsp-plugins-lv2.x86_64 lv2-calf-plugins-gui.x86_64 lv2-swh-plugins.x86_64 lv2-guitarix-plugins.x86_64 lv2-mdaEPiano.x86_64 lv2-invada-plugins.x86_64 lv2-zam-plugins.x86_64 lv2-ir-plugins.x86_64 lv2-sorcer.x86_64 lv2-vocoder-plugins.x86_64 lv2-mdala-plugins.x86_64 lv2-newtonator.x86_64 ardour7.x86_64
```

Setup realtime
===

Run this script to enable pro-audio mode.

```
#!/bin/sh

sudo cpupower frequency-set -g performance
sudo /sbin/sysctl -w vm.swappiness=10
sudo systemctl start tuned
sudo tuned-adm profile latency-performance
echo -n 3072 | sudo tee /sys/class/rtc/rtc0/max_user_freq
sudo chgrp realtime /dev/hpet /dev/rtc0
sudo chmod g+rw /dev/hpet /dev/rtc0
```