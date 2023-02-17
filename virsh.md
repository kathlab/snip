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

Get a serial console to a running VM
---

```
virsh # console VM_NAME
```

Add directory as pool
---

```
virsh # pool-define-as POOL_NAME dir - - - - "PATH"
virsh # pool-start POOL_NAME
```

Remove pool
---

```
virsh # pool-stop POOL_NAME
virsh # pool-undefine POOL_NAME
```