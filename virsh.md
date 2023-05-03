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

List all pools
---

```
virsh # pool-list --all
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

Refresh pool (Hint: ISO!)
---

```
virsh # pool-refresh POOL_NAME
```

List snapshots
---

```
virsh # snapshot-list VM_NAME
```

Create snapshot
---

```
virsh # snapshot-create-as --name "NAME" --domain VM_NAME
```

Restore to snapshot
---

```
virsh # snapshot-revert --snapshotname "NAME" VM_NAME
```

Delete snapshot
---

```
virsh # snapshot-delete --snapshotname "NAME" VM_NAME
```

Pause and resume VM
---

```
virsh # suspend VM_NAME
virsh # resume VM_NAME
```

Show all OS variants
---

```
$ virt-install --os-variant list
```

Export and import VMs
---

```
$ virsh dumpxml VM_NAME | sudo tee VM_NAME.xml
$ virsh create VM_NAME.xml
```