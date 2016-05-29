## SSH Tunnel - Local Port Forwarding 

Example: Localhost:9000
```
ssh -L 9000:imgur.com:80 user@example.com
```

Example: Terminal Service Listen in *:3390 (not only localhost)
```
ssh -nNT -L 0.0.0.0:3390:192.168.11.11:3389 jenciso@200.48.36.215
```

## SSH Tunnel - Remote Port Forwarding

Example: Expose 9000 port for public using example.com as gw

Step 1: Change sshd_config line, and restart sshd
```
GatewayPorts yes
```
Step 2: Execute
```
ssh -R 9000:localhost:3000 user@example.com
```

