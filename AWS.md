## Expandir disco

Verify if the disk is xfs type 

```
df -hT
```

Extend disk

```
lsblk
sudo growpart /dev/nvme0n1 1
sudo xfs_growfs -d /
```

https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html


## Time 

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-time.html
