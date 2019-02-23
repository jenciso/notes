## Create a ISO on Local Storage

Checked in xenserver 6.5. 

Create a directory : 
```sh
mkdir -p /var/opt/xen/ISO/nfs_sr  
```
Create an iso from the CLI :
```sh 
xe sr-create name-label=nfs-sr type=iso device-config:location=/var/opt/xen/ISO/nfs_sr device-config:legacy_mode=true content-type=iso
```  
You can see the SR in the xencenter but there will be space constraints as this will take the dom0 space/root file space. 


## Force lower memory limits in XenServer VMs

Step 1: check uuid
```bash
xe vm-list
```

Step 2: set memory limits
```bash
xe vm-param-set memory-static-min=256MiB uuid=<uuid_displayed>
```

## How to Configure dom0 Memory in XenServer 6.1 and Later

Step 1: Set memory 

```bash
/opt/xensource/libexec/xen-cmdline --set-xen dom0_mem=4096M,max:4096M
```

## PCI-Passtrough

```

Xenserver 7.1
**************
lspci

/opt/xensource/libexec/xen-cmdline --set-dom0 "xen-pciback.hide=(02:00.0)"

xe vm-list

xe vm-param-set other-config:pci=0/0000:02:00.0 uuid=7697924b-a101-0a70-d775-5070cc8aa09b

# Verify using 

xe vm-param-list uuid=7697924b-a101-0a70-d775-5070cc8aa09b

Xenserver 6.1 - PCI-Passtrough
*******************************

VT-D Unterstützung von Wirtsystem ansehen:
	xe host-dmesg | grep VT-d
	
Weitere nützliche Befehle:	
	dmesg | grep pciback
	dmesg | grep GSI | sort -u
	lspci -vt
	xl pci-list-assignable-devices
	xl dmesg
	xl dmesg | grep io
	
UUID der VM an die das Gerät durchgereicht werden soll finden:
	xe vm-list

Nummer der Geräte herausfinden
	lspci
	lspci -vt

zB:
	09:01.0 Multimedia controller: Philips Semiconductors SAA7146 (rev 01)

VM runterfahren und..

PCI-Passtrough aktivieren (1 Device):
	xe vm-param-set other-config:pci=0/0000:09:01.0 uuid=<UUID>

PCI-Passtrough deaktivieren:	
	xe vm-param-remove param-name=other-config param-key=pci uuid=<UUID>
	
xe vm-param-remove param-name=other-config param-key=pci uuid=4f65331f-61c5-8f87-6d1c-2fa401df6750
```

