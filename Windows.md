## Get users group
```
net user juan_condena /domain
```
## How long a service or process is running? (use PowerShell as Administrator

```
New-TimeSpan -Start (get-process filebeat).StartTime
```

