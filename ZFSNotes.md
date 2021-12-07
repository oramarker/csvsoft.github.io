## ZFS

```shell
zpool create -f -o ashift=12 vmdiskpool mirror /dev/nvme1n1 /dev/nvme2n1
```



## Proxmox

### Linux containers

```
pveam update
pveam  available
# then install through GUI
# once it is installed, edit the options -> features, enable 'nesting', if run docker inside the linux container.
# install docker
apt-get update
apt-get install docker.io

# start docker
systemctl enable docker
systemctl start docker

# test docker
docker run -d -p 80:80 --name myserver nginx
curl http://localhost/
```

