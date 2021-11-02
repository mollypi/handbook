# RPM

The following rpm command installs Mysql client package.

```text
# rpm -ivh  MySQL-client-3.23.57-1.i386.rpm
Preparing...################################### [100%]
   1:MySQL-client############################## [100%]
```

Query all the RPM Packages using rpm -qa You can use rpm command to query all the packages installed in your system.

```text
# rpm -qa
cdrecord-2.01-10.7.el5
bluez-libs-3.7-1.1
setarch-2.0-1.1
.
.
```

* -q query operation
* -a queries all installed packages

To identify whether a particular rpm package is installed on your system, combine rpm and grep command as shown below. Following command checks whether cdrecord package is installed on your system.

```text
# rpm -qa | grep 'cdrecord'
```

Query a Particular RPM Package using rpm -q The above example lists all currently installed package. After installation of a package to check the installation, you can query a particular package and verify as shown below.

```text
# rpm -q MySQL-client
MySQL-client-3.23.57-1
# rpm -q MySQL
package MySQL is not installed
```

* Find the package is installed

  ```text
  rpm -q package name | partial name
  ```

Examples:

```text
[ root @ localhost ~] # rpm -q gcc
gcc -4.1.2-48.el5
```

* Find the rpm package a file belongs

  ```text
  rpm -q -File File
  ```

Examples:

```text
[ root @ localhost ~] # rpm -q --file /bin/LS 
coreutils -5.97-23.el5_4.2
```

* List the rpm package has been installed in all of the files

  ```text
  rpm -a -List rpm package
  ```

Examples:

```text
[root@localhost ~]# rpm -q --list util-linux-2.13-0.52.el5_4.1
/bin/arch
/bin/dmesg
/bin/kill
/bin/logger
/bin/login
/bin/more
/bin/mount
/bin/raw
...
```

* List the contents of the file rpm

  ```text
  rpm -QPL rpm package name
  ```

  Examples:

  ```text
  [root@host1 ~]# rpm -ql grub-0.97-13.5
  /boot/grub
  /sbin/grub
  /sbin/grub-install
  /sbin/grub-md5-crypt
  /sbin/grub-terminfo
  /usr/bin/mbchk
  /usr/share/doc/grub-0.97
  /usr/share/doc/grub-0.97/AUTHORS
  /usr/share/doc/grub-0.97/COPYING
  /usr/share/doc/grub-0.97/ChangeLog
  /usr/share/doc/grub-0.97/NEWS
  /usr/share/doc/grub-0.97/README
  /usr/share/doc/grub-0.97/TODO
  ```

* Verify rpm package related files

  ```text
  rpm - V package name is already installed
  ```

Examples:

```text
[root@localhost ~]# rpm -V chkconfig-1.3.47-1.el6.x86_64
.....UG..    /etc/rc.d/init.d
```

* Recovery rpm package the relevant authority, set up group

  ```text
  rpm - SetPerms package name   # Recovery Kit permission 
  rpm - setugids   package name   # recovery package uid, gid, etc.
  ```

* Query dependencies installed rpm packages

  ```text
  rpm - qR package names
  ```

Examples:

```text
#rpm -qR which-2.16-7
/bin/sh
/bin/sh
/sbin/install-info
config(which) = 2.16-7
dev
libc.so.6()(64bit)
libc.so.6(GLIBC_2.2.5)(64bit)
libc.so.6(GLIBC_2.3)(64bit)
libc.so.6(GLIBC_2.3.4)(64bit)
libc.so.6(GLIBC_2.4)(64bit)
rpmlib(CompressedFileNames) <= 3.0.4-1
rpmlib(PayloadFilesHavePrefix) <= 4.0-1
rtld(GNU_HASH)
```

* Unzip rpm

  ```text
  rpm2cpio xxx.rpm | cpio -idv
  ```

