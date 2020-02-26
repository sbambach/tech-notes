# Linux unshare

## Description

Run program with some namespaces unshared from parent.

The unshare command is part of the util-linux package and is available from [kernel.org](https://www.kernel.org/pub/linux/utils/util-linux/).

See also man page [unshare(1)](http://man7.org/linux/man-pages/man1/unshare.1.html).

## Grant permission

```bash
sudo /sbin/sysctl -w kernel.unprivileged_userns_clone=1
```

## Overload directories (unshare --mount)

```bash
# e.g. test your nginx vhosts without breaking running nginx
$ sudo unshare --mount sh -c "mount --bind /tmp/vhosts /etc/nginx/sites-enabled && /usr/sbin/nginx -t"

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
