## Reset license windows 2012 R2

```
slmgr.vbs /rearm
```

## Download one image using PWS

```
Invoke-WebRequest -Uri https://mirror.e-unicred.com.br/repo/iso/CentOS-7-x86_64-Minimal-1804.iso -Method Get -OutFile CentOS-7-x86_64-Minimal-1804.iso -PassThru -Proxy http://192.168.0.20:8080
``` 
Note: First, you have to launch IE and enable it


## WinRM
http://www.hurryupandwait.io/blog/understanding-and-troubleshooting-winrm-connection-and-authentication-a-thrill-seekers-guide-to-adventure


## Change language in Linux System on windows 10 

```sh
sudo update-locale LANG=en_US.UTF8
```

## How can i ssh into bash on ubuntu on windows 10
```
sudo apt-get remove openssh-server
sudo apt-get install openssh-server
sudo nano /etc/ssh/sshd_config 
```
and disallow root login by setting `PermitRootLogin no`
Then add a line beneath it that says:
```
AllowUsers yourusername
```
and make sure `PasswordAuthentication is set to yes` if you want to login using a password.
Disable privilege separation by adding/modifying : `UsePrivilegeSeparation no`
```
sudo service ssh --full-restart
```
Connect to your Linux subsystem from Windows using a ssh client like PuTTY.



## Get users group
```
net user juan_condena /domain
```
## How long a service or process is running? (use PowerShell as Administrator)

```
New-TimeSpan -Start (get-process filebeat).StartTime
```

## Get the hotfix list 

* In windows command line
```
wmic qfe get Hotfixid
```
To find a specific hotfix

```
wmic qfe | find "123456"
```

* In powerShell

List all hotfix:
```
Get-Hotfix 
```
A specific KB:
```
Get-Hotfix -id kb123456
```

Another method
```
Get-ChildItem -Path “HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\HotFix”
```

## Get all member of a AD group

```
dsget group "CN=Kubernetes-cluster-admin,OU=GRUPOS,OU=example,OU=com" -members | dsget user -samid -email -upn
```
