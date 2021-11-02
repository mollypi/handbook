# Setup SSH Passwordless Login in OpenSSH

You can login to a remote Linux server without entering password in 3 simple steps using ssky-keygen and ssh-copy-id as explained in this example.

ssh-keygen creates the public and private keys. ssh-copy-id copies the local-host’s public key to the remote-host’s authorized\_keys file. ssh- copy-id also assigns proper permission to the remote-host’s home, ~/.ssh, and ~/.ssh/authorized\_keys.

## Step 1: Create public and private keys using ssh-key-gen on local-host

```text
jsmith@local-host$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key
(/home/jsmith/.ssh/id_rsa):[Enter key]
Enter passphrase (empty for no passphrase): [Press enter
key]
Enter same passphrase again: [Pess enter key]
Your identification has been saved in
/home/jsmith/.ssh/id_rsa.
Your public key has been saved in
/home/jsmith/.ssh/id_rsa.pub.
The key fingerprint is:
33:b3:fe:af:95:95:18:11:31:d5:de:96:2f:f2:35:f9
jsmith@local-host
```

## Step 2: Copy the public key to remote-host using ssh- copy-id

```text
jsmith@local-host$ ssh-copy-id -i ~/.ssh/id_rsa.pub
remote-host
jsmith@remote-host’s password:
Now try logging into the machine, with “ssh ‘remote-
host’”, and check in:
.ssh/authorized_keys to make sure we haven’t added extra
keys that you weren’t expecting.
```

Note: ssh-copy-id appends the keys to the remote-host’s .ssh/authorized\_key.

## Step 3: Login to remote-host without entering the password

```text
jsmith@local-host$ ssh remote-host
 Last login: Sun Nov 16 17:22:33 2008 from 192.168.1.2
[Note: SSH did not ask for password.] jsmith@remote-host$ [Note: You are on remote-host here]
```

