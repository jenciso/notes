##Force lower memory limits in XenServer VMs

Step 1: check uuid
```bash
xe vm-list
```

Step 2: set memory limits
```bash
xe vm-param-set memory-static-min=256MiB uuid=<uuid_displayed>
```

##How to Configure dom0 Memory in XenServer 6.1 and Later

Step 1: Set memory 

```bash
/opt/xensource/libexec/xen-cmdline --set-xen dom0_mem=4096M,max:4096M
```
