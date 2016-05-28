== Force lower memory limits in XenServer VMs

Step 1: xe vm-list
(for check uuid)

Step 2: xe vm-param-set memory-static-min=256MiB uuid=<uuid_displayed>


== How to Configure dom0 Memory in XenServer 6.1 and Later

Step 1: /opt/xensource/libexec/xen-cmdline --set-xen dom0_mem=4096M,max:4096M
