# X86 Cluster
MicroK8s on Ubuntu running on X86 Hardware/VMs

## Summary
Everything foundational with the home X86 MicroK8s cluster

### IP Address Space
Nodes: 172.16.0.30-172.16.0.39
MetalLB: 172.16.0.200-172.16.0.250

## Upgrade
```bash
microk8s kubectl drain <node> --ignore-daemonsets
apt update && apt upgrade -y
snap refresh microk8s --channel=1.28 && reboot
microk8s kubectl uncordon <node>
```

## NFS CSI Driver
https://microk8s.io/docs/how-to-nfs#install-the-csi-driver-for-nfs-2
```bash
microk8s enable helm3
microk8s helm3 repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
microk8s helm3 repo update

microk8s helm3 install csi-driver-nfs csi-driver-nfs/csi-driver-nfs \
    --namespace kube-system \
    --set kubeletDir=/var/snap/microk8s/common/var/lib/kubelet

microk8s kubectl wait pod --selector app.kubernetes.io/name=csi-driver-nfs --for condition=ready --namespace kube-system

microk8s kubectl apply -f ./NFS/sc-nfs.yaml
```

## MetalLB
https://microk8s.io/docs/addon-metallb
```bash
microk8s enable metallb:172.16.0.200-172.16.0.250
```

## Prometheus
https://www.server-world.info/en/note?os=Ubuntu_20.04&p=microk8s&f=8

## Nvidia GPU Time Slicing
https://microk8s.io/docs/addon-gpu
https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/overview.html


