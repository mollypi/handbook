# Mount

Mount a Partition After creating a partition and formatting, you can mount it to a mount point. First create a directory where the partition should be mounted.

```text
# mkdir /home/database
```

Mount the file system.

```text
# mount /dev/sda1 /home/database
```

To automatically mount the filesystem after the reboot, add the following entry to the /etc/fstab

```text
/dev/sda1 /home/database ext3 defaults 0 2
```

