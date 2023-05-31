# Create udev rules

!! Not really a good description but still useful !!

Example for Apple Magickeyboard setting FN mode by default.

Set logging
---

sudo udevadm control --log-priority=debug


Get Device Id
---

udevadm monitor


Write rule
---
vi /etc/udev/rules.d/70-magickeyboard.rules

```
ACTION=="add", SUBSYSTEM=="hidraw", KERNEL=="hidraw*", SUBSYSTEMS=="hid", KERNELS=="*:004C:026C.*", RUN+="/bin/bash -c \"echo '2' > /sys/module/hid_apple/parameters/fnmode\""
```


Reload rules
---

sudo udevadm control -R


Debug udev rule
---

sudo journalctl -f

disable bt device and check if rules file is loaded

```
/etc/udev/rules.d/70-magickeyboard.rules
```

-> check if .rules postfix is added to the filename

enable bt device

look for a line where the rule is engaged:

Nov 19 11:50:14 myhost systemd-udevd[32818]: hidraw3: /etc/udev/rules.d/70-magickeyboard.rules:1 RUN '/bin/bash -c "echo '2' > /sys/module/hid_apple/parameters/fnmode"'


looks good? check result

cat /sys/module/hid_apple/parameters/fnmode