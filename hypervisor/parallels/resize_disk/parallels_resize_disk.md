# Resizing disc within parallels (15.1.2)

## Increasing Disk Capacity (prlctl)

Source: [Increasing Disk Capacity](http://download.parallels.com/doc/pcs/html/Parallels_Cloud_Server_Users_Guide/30365.htm)

```bash
$ prlctl set my_box_name --device-set hdd0 --size 40G
Resize disk image '<parallels_dir>/my_box_name.pvm/harddisk1.hdd' up to 40960
Failed to resize: This virtual hard disk cannot be resized because it has one or more snapshots. To resize the disk, delete the snapshots and try again.
Failed to configure the virtual machine.
```

So standard resize failing, due to snapshots? Let's check...

```bash
$ prlctl snapshot-list my_box_name
PARENT_SNAPSHOT_ID                      SNAPSHOT_ID
<no snapshots found>
```

OK something is totally wrong here. Now what?!

## Unable to resize the disk because it has one or more snapshots (prl_disk_tool)

Source: [Unable to resize the disk because it has one or more snapshots](https://virtuozzosupport.force.com/s/article/000013683)

### Gather VM information

```bash
$ prlctl list "my_box_name" -i | grep pvm
Home path: <parallels_dir>/my_box_name.pvm/config.pvs
Home: <parallels_dir>/my_box_name.pvm/
  hdd0 (+) ide:0 image='<parallels_dir>/my_box_name.pvm/harddisk1.hdd' type='expanded' 32768Mb online-compact=on
```

### Gather filesystem information

Let's check for consistent snapshot data:

```bash
$ cd <parallels_dir>/my_box_name.pvm
$ find . -iname "*.hds" -o -iname "Snapshot.xml"
./harddisk1.hdd/harddisk1.hdd.0.{5fbaabe3-6958-40ff-92a7-860e329aab41}.hds
```

Having only one `*.hds` file, and no Snapshot.xml, is totally fine.

## Now let's fix your box

### Maybe a "linked" clone of a base box

*TBD*

### Multiple *.hds files an no/empty Snapshots.xml

If you have multiple `*.hds` files, but no or empty Snapshots.xml, this means you have snapshots that needs to be merged:

```bash
$ prl_disk_tool merge --hdd '<parallels_dir>/my_box_name.pvm/harddisk1.hdd'
```

### Only one *.hds file and no/empty Snapshots.xml (try to resize without merge)

```bash
$ prl_disk_tool resize --hdd '<parallels_dir>/my_box_name.pvm/harddisk1.hdd' --size 40G
Operation progress 100 %
```
