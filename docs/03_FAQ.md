## FAQ

### Could not import all dependencies
PRS requires the Python library `proxmoxer`. This can simply be installed by the most
system repositories. If you encounter this error message you simply need to install it.


```
# systemctl status proxmox-rebalancing-service
x proxmox-rebalancing-service.service - Proxmox Rebalancing Service
     Loaded: loaded (/etc/systemd/system/proxmox-rebalancing-service.service; static)
     Active: failed (Result: exit-code) since Sat 2024-07-06 10:25:16 UTC; 1s ago
   Duration: 239ms
    Process: 7285 ExecStart=/usr/bin/proxmox-rebalancing-service -c /etc/proxmox-rebalancing-service/prs.conf (code=exited, status=2)
   Main PID: 7285 (code=exited, status=2)
        CPU: 129ms

Jul 06 10:25:16 build01 systemd[1]: Started proxmox-rebalancing-service.service - Proxmox Rebalancing Service.
Jul 06 10:25:16 build01 proxmox-rebalancing-service[7285]:  proxmox-rebalancing-service: Error: [python-imports]: Could not import all dependencies. Please install "proxmoxer".
```

Debian/Ubuntu: apt-get install python3-proxmoxer
If the package is not provided by your systems repository, you can also install it by running `pip3 install proxmoxer`.
