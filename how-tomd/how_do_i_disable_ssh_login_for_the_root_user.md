# HOW DO I DISABLE SSH LOGIN FOR THE ROOT USER?

To disable root SSH login, edit /etc/ssh/sshd\_config with your favorite text editor.

```text
[root@root ~]# vi /etc/ssh/sshd_config
```

Change this line:

```text
#PermitRootLogin yes
```

Edit to this:

```text
PermitRootLogin no
```

Ensure that you are logged into the box with another shell before restarting sshd to avoid locking yourself out of the server.

```text
[root@root ~]# /etc/init.d/sshd restart
Stopping sshd: [ OK ]
Starting sshd: [ OK ]
[root@root ~]#
```

You will now be able to connect to your server via ssh with the admin user and then use the command su to switch to the root user.

