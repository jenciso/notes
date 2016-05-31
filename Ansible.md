## FOr use do sudoers with ansible
Ensure that the following line was commented 
```
#Defaults    requiretty
```

## For use CentOS 5 with ansible
Error:
```
Error: ansible requires the stdlib json or simplejson module, neither was found!
```
Solution:
```
ansible poseidon -m raw -a "sudo yum install -y python-simplejson" -u root
```

## Debug module

Example: Using debug module to display value in variable 
```
  tasks:
    - name: Set up working area
      shell: mktemp -d
      register: workdir
    - name: Working Area
      debug:
        msg: "{{ workdir }}"
```
## Restart Windows Server with Ansible

```
ansible -m raw -a 'powershell "Restart-Computer -Force"' hlg-opecrbs-web-poa
```

