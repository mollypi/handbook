# Package Manager

This guide may help people switching to a dpkg based distribution like Debian from an RPM based one like RedHat.

A quick comparison of rpm and apt/dpkg command-line arguments

List all installed packages:

```text
    rpm -qa
    dpkg --list
```

List information about an installed package:

```text
    rpm -qi pkgname
    dpkg --status pkgname     (prints a bunch of extra info too)
```

List files in an installed package

```text
    rpm -ql pkgname
    dpkg --listfiles pkgname
```

List information about a package on the local hard drive

```text
    rpm -qpi file.rpm
    dpkg --info file.deb
```

List files in a package on the local hard drive

```text
    rpm -qpl file.rpm
    dpkg --contents file.deb
```

List files in an uninstalled package \(depends on dist\)

* grep through the Contents.arch file found in ftp.us.debian.org/debian/dists/frozen/

Extract files in a package without installing it

* \(Open it in Midnight Commander \(mc\) and then enter CONTENTS.cpio\(1\)\)

  ```text
  dpkg-deb --extract file.deb dir-to-extract-to
  ```

* or Midnight Commander works on .debs too.

Install a package from a local file

```text
    rpm -i file.rpm
    dpkg --install file.deb
```

Remove a package from the system

```text
    rpm -e pkgname              (saves copies of modified config files)
    dpkg --purge pkgname        (removes everything)
    dpkg --remove pkgname       (leaves config files behind)
```

Identify the package that owns a file

```text
    rpm -qf full-path-to file
    rpm -qf name-of-file-in-local-dir
    dpkg --search any-portion-of-file's-path (2)
```

Get information about a remote package

```text
    rpm -qpi <url>
    apt-cache show package
```

List all remote packages

```text
    (browse rpmfind.net or your other site)
    apt-cache dumpavail
```

1. thanks to Adriaan Penning
2. i.e. "dpkg --search /etc" will display all packages that have files in etc.

