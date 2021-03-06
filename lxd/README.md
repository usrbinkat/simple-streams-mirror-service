## Simple Streams LXD Image Server
#### NOTICE: Container built with hard-coded host/domain/tld & IP configs for [CCIO Mini-Stack](https://github.com/containercraft/mini-stack)    

#### Create the Container Profile
```sh
wget -O- https://gitlab.com/kat.morgan/simple-streams-mirror-build/raw/master/lxd/aux/bin/profile-build-lxd-simple-streams.sh | bash
```
#### Create the Container
```sh
lxc launch ubuntu:bionic simple-streams -p simple-streams
lxc exec simple-streams -- tail -f /var/log/cloud-init-output.log
```

#### Sync Images:
```sh
lxc exec simple-streams -- /bin/bash -c "sudo sstream-mirror --keyring=/usr/share/keyrings/ubuntu-cloudimage-keyring.gpg https://cloud-images.ubuntu.com/releases/ /var/www/html/cloud-images/ 'arch=amd64' 'release~(trusty|xenial|bionic)' --max=1 --progress"
```

## Configure LXD Client:
#### NOTE: Create DNS record for 'lxd.mirror.mini-stack.maas' or manual 'hosts' entry

#### Import Certificate
```sh
mkdir /usr/local/share/ca-certificates/mirror
wget -P /usr/local/share/ca-certificates/mirror/ https://lxd.mirror.mini-stack.maas/lxd-mirror-ca.crt
chmod 644 /usr/local/share/ca-certificates/mirror/lxd-mirror-ca.crt
update-ca-certificates
```
#### Import Mirror
```sh
lxc remote add mirror https://lxd.mirror.mini-stack.maas/lxd-mirror-ca.crt --protocol=simplestreams
```
#### Launch Image
```sh
lxc launch mirror:bionic mirror-test
```
