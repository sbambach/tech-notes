# IOCage setup within FreeNAS

## MySQL

```bash
JNAME=mysql
MACS="5480289de610 5480289de710"
echo '{"pkgs":["bash","htop","wget","curl","nano","vim","git","portmaster","ca_root_nss"]}' > /tmp/pkg.json
RELEASE="11.2-RELEASE"
iocage create -n ${JNAME} -p /tmp/pkg.json -r "${RELEASE}" dhcp=on vnet=on vnet0_mac="${MACS}" interfaces=vnet0:bridge11 vnet_default_interface=none allow_raw_sockets=1 boot=on
iocage exec ${JNAME} "sed -i '' -e 's/quarterly/latest/g' /etc/pkg/FreeBSD.conf"
iocage exec ${JNAME} "pkg update && pkg upgrade -y"
iocage exec ${JNAME} "portsnap fetch extract"
iocage exec ${JNAME} "pkg install -y percona57-server"
iocage fstab -a ${JNAME} /mnt/mypool/services/mysql/var_db_mysql /var/db/mysql nullfs rw 0 0
iocage exec ${JNAME} "sysrc mysql_enable=\"yes\""
iocage exec ${JNAME} "service mysql-server start"
```

## GIT data store (used by gitlab iocage)

```bash
JNAME=git
MACS="5480289de611 5480289de711"
iocage fstab -a ${JNAME} /mnt/mypool/services/git/usr_local_git_repositories /usr/local/git/repositories nullfs rw 0 0
```

## Backup

```bash
JNAME=backup
MACS="5480287cf969 5480287cf96a"
echo '{"pkgs":["bash","htop","wget","curl","nano","vim","git","portmaster","ca_root_nss"]}' > /tmp/pkg.json
RELEASE="11.3-RELEASE"
iocage create -n ${JNAME} -p /tmp/pkg.json -r "${RELEASE}" dhcp=on vnet=on vnet0_mac="${MACS}" interfaces=vnet0:bridge11 vnet_default_interface=none allow_raw_sockets=1 boot=on
iocage exec ${JNAME} "sed -i '' -e 's/quarterly/latest/g' /etc/pkg/FreeBSD.conf"
iocage exec ${JNAME} "pkg update && pkg upgrade -y"
iocage exec ${JNAME} "portsnap fetch extract"
iocage exec backup "pkg install -y py37-borgbackup rclone"
iocage exec backup "sysrc fuse_load=\"YES\""
```
