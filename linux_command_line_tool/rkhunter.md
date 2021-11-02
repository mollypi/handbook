# RKhunter

rkhunter "debugging" howto 1. Don't be afraid of the RKhunter warnings in the terminal. 2. Using RKhunter is always a work in progress. 3. To install RKhunter:

`sudo apt-get install rkhunter`

1. Before running RKhunter you will need to fill the file properties database by running the following command: rkhunter --propupd Do no forget to set rkhunter in sysconfig to run the --propupd every time new software is installed or else you will get "false positives" after every software and system update.

`sudo rkhunter --propupd`

1. To run rkhunter --propupd, automatic after software updates, add the line APT\_AUTOGEN="yes" to /etc/default/rkhunter \(this gets read by /etc/apt/apt.conf.d/90rkhunter\).
2. Wait till it completes gathering the new values, then exit. This should eliminate all the warnings except the hidden files related to the /dev folder. They show up occassionally and disappear with a next reboot of your system.
3. Additionally, the --versioncheck option of rkhunter itself will indicate if a new version is available.

`sudo rkhunter --versioncheck`

1. The first run of 'rkhunter' after installation may give some warning messages. They are is some way normal. Even on clean installed system, with no additional software installed, these warnings occur. You could take a at the FAQ of RKhunter. I got these warnings on Xubuntu beta, clean install:

`sudo rkhunter --checkall`

## warnings:

```text
/usr/bin/mail

/usr/bin/bsd/mail-x

checking /dev for susp. files

checking hidden files and direct

/usr/bin/lwp-request
```

1. It is possible for a package manager database to become maliciously corrupted. RKhunter can only report on changes, but not on what has caused the change, it is reactive.
2. Help Rootkit Hunter users on the rkhunter-users mailing list. the rkhunter mailinglist It is also a source of information on "false positives".
3. "Intruder Detection Checklist". This list is available via the intruder detection list
4. What to do with "common" warnings as:

```text
Warning: Hidden directory found: /dev/.static

Warning: Hidden directory found: /dev/.udev

Warning: Hidden directory found: /dev/.initramfs
```

To avoid these warnings, you can reconfigure rkhunter to ignore these files via whitelisting these warnings. Edit the rkhunter.conf file: gedit /etc/rkhunter.conf and remove the \# in front of these lines:

```text
#ALLOWHIDDENDIR=/dev/.udev

#ALLOWHIDDENDIR=/dev/.static

#ALLOWHIDDENDIR=/dev/.initramfs

ALLOWHIDDENDIR=/dev/.udev

ALLOWHIDDENDIR=/dev/.static

ALLOWHIDDENDIR=/dev/.initramfs
```

1. Linkage for debugging rkhunter via watchdog: debugging linkage You can disable the 'os\_specific' check in your rkhunter.conf file. Add it to the DISABLE\_TESTS list.You can stop rkhunter from checking these by editing /etc/rkhunter.conf

Un-comment the related ALLOWHIDDENDIR and ALLOWHIDDENFILE lines.

