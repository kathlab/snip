# Virsh KVM console

Show all defined VMs
---

```
virsh # list --all
```

Start and stop VM
---

__Note:__ _destroy_ may sound harmful, but the command only stops a VM instance.

```
virsh # start VM_NAME
virsh # destroy VM_NAME
```

Delete VM (=undefine)
---

```
virsh # undefine VM_NAME
```

Edit VM parameters
---

```
virsh # edit VM_NAME
```
