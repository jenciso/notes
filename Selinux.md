## Search audit logs

All logs:
```
ausearch -m ALL
```
specific, like AVC, hyperv, etc

```
ausearch -m avc
```

## Elemental packages to control selinux via ansible
```
yum -y install libselinux-python
yum -y install policycoreutils-python
```

## Add the httpd_t domain in mode permissive 
```
semanage permissive -a httpd_t
```

## List all domains with permissive mode selinux
```
semodule -l | grep permissive
```

## Delete the httpd_t domain in mode permissive
```
semanage permissive -d httpd_t
```
## Create policy
https://www.nixpal.com/selinux-security/

## Why was blocked?
```
cat /var/log/audit/audit.log | audit2why 
```
