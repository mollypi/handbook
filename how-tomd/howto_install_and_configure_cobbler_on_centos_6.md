# Howto Install and Configure Cobbler on Centos 6

This document describes the steps to get cobbler up and running on Centos 6 After the installation finnishes we need to perform the following steps before we can install Cobbler:

1. Configure the network interfaces 2. Configure the resolver
2. Disable Selinux
3. Disable Iptables Firewall
4. Install EPEL Yum Repository

## Configure Linux

## Configure the network interfaces

In Redhat 6 Network configurations are handled by network-manager. Since I always install a minimalistic server network-manager is by default not installed and configuration should be done the old fashioned way by edditing the configuration files directly. First we set the correct hostname and gateway

```text
vi /etc/sysconfig/network
```

## Configure the resolver

## Disable selinux

Change the following line from:

```text
SELINUX=enforcing
```

to:

```text
SELINUX=disabled
```

## Disable iptables

Stop Iptables:

```text
service iptables stop
```

Disable iptables:

```text
chkconfig iptables off
```

## Install the Fedora EPEL repository

```text
rpm -Uhv
http://download.fedora.redhat.com/pub/epel/6/i386/epel-release-6-5.noarch.rp
m
```

## Install Cobbler and prerequisites

Install the required packages:

```text
yum install cobbler cobbler-web pykickstart
```

Enable the required services:

```text
chkconfig httpd on
chkconfig xinetd on
chkconfig cobblerd on
```

Start the required services:

```text
service httpd start
service xinetd start
service cobblerd start
```

## Configure Cobbler and prerequisites

## Install and Configure ISC Dhcp and Bind

For a complete standalone install server we need to install a local dhcp server and a dns server, we will use the bind dns software and the dhcp software both from ISC. Bind has a track record of being hard to configure because of the miriad of options but since cobbler does all the configuring for us we dont need to worry about that.

First install Bind and Dhcp

```text
yum install -y bind bind-utils dhcp
```

Enable Bind

```text
chkconfig named on
```

Start Bind

```text
service named start
```

Enable Dhcp

```text
chkconfig dhcpd on
```

Start Dhcp

```text
service dhcpd start
```

Enable dns and dhcp management in cobbler

```text
vi /etc/cobbler/modules.conf
```

and configure the dns and dhcp lines to look like the example below

```text
[dns]
module = manage_bind
[dhcp]
module = manage_isc
```

Configuring Bind and Dhcp is done via cobbler, open the following files and make the the changes as stated below:

```text
vi /etc/cobbler/dhcp.template
```

Make sure the following lines match your network configuration

```text
subnet 192.168.1.0 netmask 255.255.255.0 {
     option routers             192.168.1.1;
     option domain-name-servers 192.168.1.1;
     option subnet-mask         255.255.255.0;
     range dynamic-bootp        192.168.1.100 192.168.1.254;
```

After the above changes it's time to restart cobbler

```text
service cobblerd restart
```

All changes are now active, let's check if cobbler has any recomendations to get things working:

```text
cobbler check
```

Correct settings sugested by Cobbler check command

```text
vi /etc/cobbler/settings.conf
```

change the following lines in the settings file to match the example below

```text
server: 192.168.1.66
next_server: 192.168.1.66
```

Now we need to install some network bootloaders

```text
cobbler get-loaders
```

This will download all required boot loaders and some more exotic boot loaders for other architectures. Enable the tftpd daemon in xinetd

```text
vi /etc/xinetd.d/tftp
```

And change the line:

```text
disable = yes
```

to:

```text
disable = no
```

Enable the Rsync daemon in xinetd

```text
vi /etc/xinetd.d/rsync
```

And change the line:

```text
disable = yes
```

to:

```text
disable = no
```

Now we can restart xinetd

```text
service xinetd restart
```

