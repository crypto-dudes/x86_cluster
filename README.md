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

# Kubernetes Secrets for use by Services

## InfluxDB

```bash
 kubectl -n crypto create secret generic influxcred \
    --from-literal=url="http://influxdb.home.diecksystems.com:8086" \
    --from-literal=token='CHANGEME'
```

```bash
kubectl -n crypto create secret generic init-influxdb2-cred \
  --from-literal=username="admin" \
  --from-literal=password="CHANGEME" \
  --from-literal=org="crypto" \
  --from-literal=bucket="1inch-dev" \
  --from-literal=retention="10y" \
  --from-literal=admin-token="CHANGEME"
```

## Redis

```bash
 kubectl -n crypto create secret generic rediscred \
  --from-literal=host="redis.home.diecksystems.com" \
  --from-literal=port="6379" \
  --from-literal=database="0" \
  --from-literal=username="default" \
  --from-literal=password="CHANGEME"
```

## Github

```bash
kubectl -n crypto create secret generic ghcrcred \
    --type=kubernetes.io/dockerconfigjson \
    --from-file=.dockerconfigjson=$HOME/Code/x86_cluster/ghcr.json
```

## 1inch
Comma separated list of API keys

```bash
kubectl -n crypto create secret generic oneinch-creds \
  --from-literal=tokens="[API TOKENS]" \
  --from-literal=url="https://api.1inch.dev"
```

## Wallet
```bash
 kubectl -n crypto create secret generic wallet-address-dev \
  --from-literal=address="[HEX ADDRESS]" \
  --from-literal=key="[PRIVATE KEY]"
```