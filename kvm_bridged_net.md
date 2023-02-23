# Bridged networking with KVM

1. Create bridge network device with NetworkManager
---

Run the terminal app for super easy bridge setup:

```
sudo nmtui
```
Leave your ethernet device as-is:
![](assets/kvm_bridge3.png "")

Create a new connection:
![](assets/kvm_bridge0.png "")

Select _Add_ and _Bridge_ as a new connection type:
![](assets/kvm_bridge1.png "")
![](assets/kvm_bridge2.png "")

Set a nice profile name and enter a device name for the bridge. Then add a new ethernet connection that the bridge can use to communicate with the physical network:
![](assets/kvm_bridge2b.png "")

Select _Show_ to open the IP configuration and setup the IP address for the bridge device. Add a gateway if the ethernet device you've selected is your main internet connection:
![](assets/kvm_bridge4b.png "")

Check _Automatially connect_ and _Available to all users_:
![](assets/kvm_bridge5.png "")

Next, activate the bridge:
![](assets/kvm_bridge6.png "")
![](assets/kvm_bridge7.png "")



2. Create XML bridge file
---

This file describes a network _kvm_bridge_ that works as a bridge.

```
<network>
    <name>kvm_bridge</name>
    <forward mode="bridge" />
    <bridge name="br0" />
</network>
```


2. Create bridge network in virsh
---

```
virsh # net-define bridge.xml
virsh # net-list --all
virsh # net-start kvm_bridge
virsh # net-autostart kvm_bridge
```

3. Change VM network to bridge
---

```
virsh # edit VM_NAME
```

Change bridge from _virbr0_ to _br0_:

![](assets/kvm_bridge_virsh.png "")