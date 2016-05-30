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
