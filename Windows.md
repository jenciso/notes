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
```
Get-Hotfix -id kb123456
```

