# PI Cluster

## Summary
Everything foundational with the home Pi MicroK8s cluster

## Upgrade
```bash
microk8s kubectl drain <node> --ignore-daemonsets
apt update && apt upgrade -y
snap refresh microk8s --channel=1.28 && reboot
microk8s kubectl uncordon <node>
```