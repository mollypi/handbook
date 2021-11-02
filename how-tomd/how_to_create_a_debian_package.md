# How to create a Debian package

## 1.- The package creation process

There are several tools involved in this process, used to build, check and sign the package. Debuild is a wrapper that will call them appropriately, so we don't need to do it manually. Here is a brief description of the tools it invokes:

* dpkg-buildpackage: It creates a temporary directory with the package files, building later the .deb package with its content. To work properly, it heavily relies on the files in the special debian subdirectory: control, rules, changelog, etc.
* lintian: Dissects Debian packages trying to find bugs or policy violations.
* debsign: Signs packages \(.dsc and .changes files\) using GPG or PGP.

## 2.- Installing necessary software to build our packages

```bash
 apt-get install dh-make build-essential
 apt-get install devscripts fakeroot debootstrap pbuilder
```

## 3.- Setting up environment variables

```bash
 DEBEMAIL="your_email_address@domain.com"  
 DEBFULLNAME="Your Name"  
 export DEBEMAIL DEBFULLNAME
```

## 4.- Uncompressing our source code \(format of the tar.gz file is software-version.tar.gz\)

In my case, for the purpose of this little how-to, I will build the Debian package for a simple "hello world" program written in C.

```bash
 root@debian-package:/opt# tar -xvzf hello-0.1.tar.gz   
 hello-0.1/  
 hello-0.1/Makefile  
 hello-0.1/hello_world.c
```

hello\_world.c:

```bash
 #include <stdio.h>  
 main ()  
 {  
      printf("Hello World");  
 }
```

Makefile:

```bash
 DESTDIR=/  
 INSTALL_LOCATION=$(DESTDIR)/usr/  
 CFLAGS:=$(shell dpkg-buildflags --get CFLAGS)  
 LDFLAGS:=$(shell dpkg-buildflags --get LDFLAGS)  
 all: hello_world  
 hello_world: hello_world.o  
      cc $(CFLAGS) $(LDFLAGS) -o $@ hello_world.o  
 install: hello_world_install  
 hello_world_install:  
      mkdir -p $(INSTALL_LOCATION)/bin  
      cp hello_world $(INSTALL_LOCATION)/bin  
      chmod 755 $(INSTALL_LOCATION)/bin/hello_world  
 clean:  
      rm -f *.o hello_world
```

There are a two interesting things that we can see in our Makefile:

* It uses DESTDIR variable to support the DESTDIR convention. 
* Dpkg-buildflags is used to get C compiler options \(CFLAGS\) as well as linker options \(LDFLAGS\). This complies with the hardening requirements described in Debian documentation.

On the other hand, if your software has any external dependencies, you would need to install those, so you can compile it successfully. Typically you would be able to install them using apt-get.

## 5.- Building the Debian files skeleton

```bash
 root@debian-package:/opt# cd hello-0.1  
 root@debian-package:/opt/hello-0.1# dh_make -f ../hello-0.1.tar.gz   
 Type of package: single binary, indep binary, multiple binary, library, kernel module, kernel patch?  
  [s/i/m/l/k/n] s  
 Maintainer name : Your name
 Email-Address  : your_email_address@domain.com   
 Date       : Tue, 24 Jun 2014 21:50:02 +0000  
 Package Name   : hello  
 Version     : 0.1  
 License     : blank  
 Type of Package : Single  
 Hit <enter> to confirm:   
 Done. Please edit the files in the debian/ subdirectory now. You should also  
 check that the hello Makefiles install into $DESTDIR and not in / .
```

As we only want to build a single binary package, I chose that option. Multiple binary package option would in fact, build multiple .deb packages.

Now we have a new directory, called "debian", with all the necessary Debian files that we need to build our package, including examples. This includes important files like:

* control: includes meta data about the package
* rules: specifies how the package is going to be built
* changelog: history of the debian package
* copyright: copyright information

As well, some other example files are created by dh\_make, that we won't use at this point and can be deleted safely.

```bash
 root@debian-package:/opt/hello-0.1/debian# rm -f *.ex *.EX README.*  
 root@debian-package:/opt/hello-0.1/debian# ls  
 changelog compat control copyright docs rules source
```

## 6.- Control file

The control file has two sections, the first part refers to the source package and the second to the binary one. More information about the different fields can be found in deb-control manual page.

```bash
 Source: hello  
 Maintainer: Your Name <your_email_address@domain.com>   
 Build-Depends: debhelper (>= 8.0.0)   
 Standards-Version: 3.9.3   
 Section: utils

 Package: hello  
 Priority: extra  
 Architecture: any   
 Depends: ${shlibs:Depends}, ${misc:Depends}  
 Description: Test package for hello world  
  This software literally prints "hello world".
```

The variable ${shlibs:Depends} will be substituted by the shared library dependencies needed to build our binary package. Those are calculated automatically by dh\_shlibdeps, one of the tools of the debhelper suite.

## 7.- Changelog file

```bash
 root@debian-package:/opt/hello-0.1# cat debian/changelog   
 hello (0.1-1) unstable; urgency=low  
  * Initial release (Closes: #100) # there was no previous ITP  
  -- Your Name <your_email_address@domain.com> Wed, 25 Jun 2014 19:50:08 +0000
```

ITP stands for Intend to Package and, for our package to be included in a Debian distribution, the changelog file should close an existing bug. For our example we closed bug \#100, this way we won't see lintian warnings requiring for this number later.

Note: We can use "dch -i" command to edit our changelog file.

## 8.- Copyright file

```bash
 root@debian-package:/opt/hello-0.1# cat debian/copyright   
 Format: http://www.debian.org/doc/packaging-manuals/copyright-format/1.0/  
 Upstream-Name: hello  
 Files: *  
 Copyright: 2014 Your Name <your_email_address@domain.com>  
 License: GPL-2  
  This package is free software; you can redistribute it and/or modify  
  it under the terms of the GNU General Public License as published by  
  the Free Software Foundation; either version 2 of the License, or  
  (at your option) any later version.  
  .  
  This package is distributed in the hope that it will be useful,  
  but WITHOUT ANY WARRANTY; without even the implied warranty of  
  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the  
  GNU General Public License for more details.  
  .  
  You should have received a copy of the GNU General Public License  
  along with this program. If not, see <http://www.gnu.org/licenses/>  
  .  
  On Debian systems, the complete text of the GNU General  
  Public License version 2 can be found in "/usr/share/common-licenses/GPL-2".
```

## 9.- Rules file

The rules file invokes the original software Makefile script, as well as the debhelper suite of tools \(with the prefix "dh\_\). These tools handle different tasks, including the creation of the .deb file \(dh\_builddeb\).

```bash
 #!/usr/bin/make -f  
 # -*- makefile -*-  
 # Uncomment this to turn on verbose mode.  
 #export DH_VERBOSE=1  
 %:  
     dh $@
```

The rules file can be run with different targets: clean \(invokes make clean\), build \(invokes make\) and binary \(invokes make install\). Usage of fakeroot command is recommended so you don't need to build your packages as root.

```bash
root@debian-package:/opt/hello-0.1# fakeroot debian/rules clean  
 dh clean   
   dh_testdir  
   dh_auto_clean  
 make[1]: Entering directory `/opt/hello-0.1'  
 rm -f *.o hello_world   
 make[1]: Leaving directory `/opt/hello-0.1'  
   dh_clean  
 root@debian-package:/opt/hello-0.1# fakeroot debian/rules build  
 dh build   
   dh_testdir  
   dh_auto_configure  
   dh_auto_build  
 make[1]: Entering directory `/opt/hello-0.1'  
 cc -g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security  -c -o hello_world.o hello_world.c  
 cc -g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security -Wl,-z,relro -o hello_world hello_world.o  
 make[1]: Leaving directory `/opt/hello-0.1'  
   dh_auto_test  
 root@debian-package:/opt/hello-0.1# fakeroot debian/rules binary  
 dh binary   
   dh_testroot  
   dh_prep  
   dh_installdirs  
   dh_auto_install  
 make[1]: Entering directory `/opt/hello-0.1'  
 mkdir -p /opt/hello-0.1/debian/hello/usr//bin  
 cp hello_world /opt/hello-0.1/debian/hello/usr//bin  
 chmod 755 /opt/hello-0.1/debian/hello/usr//bin/hello_world  
 make[1]: Leaving directory `/opt/hello-0.1'  
   dh_install  
   dh_installdocs  
   dh_installchangelogs  
   dh_installexamples  
   dh_installman  
   dh_installcatalogs  
   dh_installcron  
   dh_installdebconf  
   dh_installemacsen  
   dh_installifupdown  
   dh_installinfo  
   dh_pysupport  
 dh_pysupport: This program is deprecated, you should use dh_python2 instead. Migration guide: http://deb.li/dhs2p  
   dh_installinit  
   dh_installmenu  
   dh_installmime  
   dh_installmodules  
   dh_installlogcheck  
   dh_installlogrotate  
   dh_installpam  
   dh_installppp  
   dh_installudev  
   dh_installwm  
   dh_installxfonts  
   dh_installgsettings  
   dh_bugfiles  
   dh_ucf  
   dh_lintian  
   dh_gconf  
   dh_icons  
   dh_perl  
   dh_usrlocal  
   dh_link  
   dh_compress  
   dh_fixperms  
   dh_strip  
   dh_makeshlibs  
   dh_shlibdeps  
   dh_installdeb  
   dh_gencontrol  
 dpkg-gencontrol: warning: File::FcntlLock not available; using flock which is not NFS-safe  
   dh_md5sums  
   dh_builddeb  
 dpkg-deb: building package `hello' in `../hello_0.1-1_amd64.deb'.
```

At this point we have already our .deb file created!. We can see the shared libraries needed to build our program \(in this case libc6, because of the stdio.h include\) listed in our debian/hello.substvars file:

```bash
 root@debian-package:/opt/hello-1.0# cat debian/hello.substvars   
 shlibs:Depends=libc6 (>= 2.2.5)  
 misc:Depends=
```

## 10.- Inspecting package contents

```bash
 root@debian-package:/opt/hello-0.1# find debian/hello  
 debian/hello  
 debian/hello/DEBIAN  
 debian/hello/DEBIAN/control  
 debian/hello/DEBIAN/md5sums  
 debian/hello/usr  
 debian/hello/usr/bin  
 debian/hello/usr/bin/hello_world  
 debian/hello/usr/share  
 debian/hello/usr/share/doc  
 debian/hello/usr/share/doc/hello  
 debian/hello/usr/share/doc/hello/copyright  
 debian/hello/usr/share/doc/hello/changelog.Debian.gz  
 root@debian-package:/opt/hello-0.1# dpkg --contents ../hello_0.1-1_amd64.deb   
 drwxr-xr-x root/root     0 2014-07-02 02:07 ./  
 drwxr-xr-x root/root     0 2014-07-02 02:07 ./usr/  
 drwxr-xr-x root/root     0 2014-07-02 02:07 ./usr/bin/  
 -rwxr-xr-x root/root   6160 2014-07-02 02:07 ./usr/bin/hello_world  
 drwxr-xr-x root/root     0 2014-07-02 02:07 ./usr/share/  
 drwxr-xr-x root/root     0 2014-07-02 02:07 ./usr/share/doc/  
 drwxr-xr-x root/root     0 2014-07-02 02:07 ./usr/share/doc/hello/  
 -rw-r--r-- root/root    940 2014-07-02 01:12 ./usr/share/doc/hello/copyright  
 -rw-r--r-- root/root    174 2014-06-30 23:51 ./usr/share/doc/hello/changelog.Debian.gz
```

## 11.- Package maintenance scripts

It is possible to supply scripts that will run when the package is installed, upgraded or removed. These scripts are the control information files: preinst, postinst, prerm, postrm. And in some cases, may prompt the user if necessary, typically through a program such as debconf. More information on how to create this scripts can be found at the Debian Policy Manual.

## 12.- Debuild

As mentioned before, we can use debuild to build the Debian binary and source packages, check it with lintian, and sign it with debsign. We can use "debuild -us -uc" to build the packages without signing the .changes file. More information can be found with "man debuild".

```bash
root@debian-package:/opt/hello-0.1# debuild -us -uc  
  dpkg-buildpackage -rfakeroot -D -us -uc  
 dpkg-buildpackage: warning: using a gain-root-command while being root  
 dpkg-buildpackage: source package hello  
 dpkg-buildpackage: source version 0.1-1  
 dpkg-buildpackage: source changed by Your Name <your_email_address@domain.com>  
  dpkg-source --before-build hello-0.1  
 dpkg-buildpackage: host architecture amd64  
  fakeroot debian/rules clean  
 dh clean   
   dh_testdir  
   dh_auto_clean  
 make[1]: Entering directory `/opt/hello-0.1'  
 rm -f *.o hello_world   
 make[1]: Leaving directory `/opt/hello-0.1'  
   dh_clean  
  dpkg-source -b hello-0.1  
 dpkg-source: info: using source format `3.0 (quilt)'  
 dpkg-source: info: building hello using existing ./hello_0.1.orig.tar.gz  
 dpkg-source: info: building hello in hello_0.1-1.debian.tar.gz  
 dpkg-source: info: building hello in hello_0.1-1.dsc  
  debian/rules build  
 dh build   
   dh_testdir  
   dh_auto_configure  
   dh_auto_build  
 make[1]: Entering directory `/opt/hello-0.1'  
 cc -g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security  -c -o hello_world.o hello_world.c  
 cc -g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security -Wl,-z,relro -o hello_world hello_world.o  
 make[1]: Leaving directory `/opt/hello-0.1'  
   dh_auto_test  
  fakeroot debian/rules binary  
 dh binary   
   dh_testroot  
   dh_prep  
   dh_installdirs  
   dh_auto_install  
 make[1]: Entering directory `/opt/hello-0.1'  
 mkdir -p /opt/hello-0.1/debian/hello/usr//bin  
 cp hello_world /opt/hello-0.1/debian/hello/usr//bin  
 chmod 755 /opt/hello-0.1/debian/hello/usr//bin/hello_world  
 make[1]: Leaving directory `/opt/hello-0.1'  
   dh_install  
   dh_installdocs  
   dh_installchangelogs  
   dh_installexamples  
   dh_installman  
   dh_installcatalogs  
   dh_installcron  
   dh_installdebconf  
   dh_installemacsen  
   dh_installifupdown  
   dh_installinfo  
   dh_pysupport  
 dh_pysupport: This program is deprecated, you should use dh_python2 instead. Migration guide: http://deb.li/dhs2p  
   dh_installinit  
   dh_installmenu  
   dh_installmime  
   dh_installmodules  
   dh_installlogcheck  
   dh_installlogrotate  
   dh_installpam  
   dh_installppp  
   dh_installudev  
   dh_installwm  
   dh_installxfonts  
   dh_installgsettings  
   dh_bugfiles  
   dh_ucf  
   dh_lintian  
   dh_gconf  
   dh_icons  
   dh_perl  
   dh_usrlocal  
   dh_link  
   dh_compress  
   dh_fixperms  
   dh_strip  
   dh_makeshlibs  
   dh_shlibdeps  
   dh_installdeb  
   dh_gencontrol  
 dpkg-gencontrol: warning: File::FcntlLock not available; using flock which is not NFS-safe  
   dh_md5sums  
   dh_builddeb  
 dpkg-deb: building package `hello' in `../hello_0.1-1_amd64.deb'.  
  dpkg-genchanges >../hello_0.1-1_amd64.changes  
 dpkg-genchanges: warning: missing Priority for source files  
 dpkg-genchanges: including full source code in upload  
  dpkg-source --after-build hello-0.1  
 dpkg-buildpackage: full upload (original source is included)  
 Now running lintian...  
 warning: the authors of lintian do not recommend running it with root privileges!  
 W: hello: binary-without-manpage usr/bin/hello_world  
 Finished running lintian.
```

Both source and binary packages have been built now. One of Lintian checks warned us about our package not including a manual page for hello\_world binary. Although it is just a reminder, more information about this warning can be displayed using "lintian-info -t lintian\_tag" command. In our case lintian\_tag is "binary-without-manpage".

```bash
 root@debian-package:/opt/hello-0.1# ls -l ../  
 total 32  
 drwxr-xr-x 3 root root 4096 Jul 2 03:07 hello-0.1  
 -rw-r--r-- 1 root root 3020 Jul 2 03:09 hello_0.1-1_amd64.build  
 -rw-r--r-- 1 root root 1407 Jul 2 03:08 hello_0.1-1_amd64.changes  
 -rw-r--r-- 1 root root 3338 Jul 2 03:08 hello_0.1-1_amd64.deb  
 -rw-r--r-- 1 root root 1378 Jul 2 03:07 hello_0.1-1.debian.tar.gz  
 -rw-r--r-- 1 root root 735 Jul 2 03:07 hello_0.1-1.dsc  
 -rw-r--r-- 1 root root 441 Jun 30 23:43 hello_0.1.orig.tar.gz  
 -rw-r--r-- 1 root root 441 Jun 30 23:43 hello-0.1.tar.gz
```

## 13.- Extracting sources

Finally we can use dpkg-source command to extract the sources from our package:

```bash
 root@debian-package:/opt# dpkg-source -x hello_0.1-1.dsc
 dpkg-source: warning: extracting unsigned source package (hello_0.1-1.dsc)  
 dpkg-source: info: extracting hello in hello-0.1  
 dpkg-source: info: unpacking hello_0.1.orig.tar.gz  
 dpkg-source: info: unpacking hello_0.1-1.debian.tar.gz
```

