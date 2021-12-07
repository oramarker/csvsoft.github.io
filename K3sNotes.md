## K3s

### Installation

```
curl -sfL https://get.k3s.io | sh -

# cat /var/lib/rancher/k3s/server/node-token
# worker nodes
curl -sfL https://get.k3s.io | K3S_URL=https://localhost:6443 K3S_TOKEN=K10a0d5ad75fc850c8c70d29593d5e45961b71851ea595ef77060b546bbf0d27956::server:7f74523abc7862b5e0bd49b04c5e6109 sh -
```



### Arkad

```
curl -sSL https://dl.get-arkade.dev | sudo sh
```





## MINIO



 Install krew, the kubectl plugin manager: https://krew.sigs.k8s.io/docs/user-guide/setup/install/

```
# install git
apt-get install git
# run below command
set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/krew.tar.gz" &&
  tar zxvf krew.tar.gz &&
  KREW=./krew-"${OS}_${ARCH}" &&
  "$KREW" install krew

```



### Install MINIO Operator

```
kubectl krew update
kubectl krew install minio
```



1. Create namespace

   ```
   kubectl create namespace minio-tenant-1	
   ```

   

2. Create a local storage Storage class with the following yaml

   

   ```yaml
   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
      name: local-storage
   provisioner: kubernetes.io/no-provisioner
   volumeBindingMode: WaitForFirstConsumer
   ```

   