# Setup netclient on Alpine Linux

This repository is designed to make netclient run on Alpine Linux, so that a Proxmox VE Server could run a much smaller LXC Container to run netmaker.

## Pre-requisites

### Container Host setup

If you want to setup netclient in container. Wireguard module need to be loaded on the host.
```
# On PVE:
apt install wireguard
modprobe wireguard
# Load the module on boot
echo "wireguard" >> /etc/modules-load.d/modules.conf
```

### Dependensies

(For chinese users) Use TUNA mirrors
```
sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories
```
Install dependensies
```
# Run one of the following commands
# For running in container (LXC / docker)
apk add --no-cache wireguard-tools
# For lts kernel
apk add --no-cache wireguard-tools wireguard-lts
# For virt kernel
apk add --no-cache wireguard-tools wireguard-virt
```

## Install netclient

Get netclient
```
wget -O netclient https://github.com/gravitl/netmaker/releases/download/v0.12.2/netclient
chmod +x netclient
mv netclient /sbin/netclient
```
**Copy the scripts from this repo to coresponding location**
Configure logrotate and OpenRC
```
rc-update add netclient
apk add --no-cache logrotate
```

Create folders required by netclient
```
# Dummy systemd folder
mkdir -p /etc/systemd/system
mkdir -p /etc/netclient/config
mkdir -p /var/log/netclient
```

Start the service
```
rc-service netclient start
```

Join network
```
netclient join -t <Access Token>
rc-service netclient restart
```
