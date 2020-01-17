## Ansible Reboot controlled by wait_for

Old way to restart within a task and continue the playbook

```
- name: Reboot system
  shell: sleep 2 && shutdown -r now
  async: 5
  poll: 0
  when: tasks_result.changed

- name: Wait for system to come back up
  wait_for:
    timeout: 60
    host: "{{ ansible_host }}"
    port: "{{ ansible_port }}"
    search_regex: OpenSSH
    delay: 15
  delegate_to: localhost
  when: tasks_result.changed
```
Source: https://dmsimard.com/2016/03/15/changing-the-ssh-port-with-ansible/
