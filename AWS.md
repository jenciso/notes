## Describe EC2 instance using a tag 

```
aws ec2 describe-instances --filters "Name=tag:Name,Values=mail"
aws ec2 describe-instances --filters "Name=tag-value,Values=mail"
```


## Start Instances based on tag

```
aws ec2 start-instances --instance-ids $(aws ec2 describe-instances --filters "Name=tag:Name,Values=bastion-aws" | jq -r .Reservations[].Instances[].InstanceId)
```

## Change template version 

```
aws ec2 modify-launch-template --launch-template-id "lt-025e4c13df8edace4" --default-version "4" --region "us-east-1"
```

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
