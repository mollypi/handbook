# Create a New User

**usually the first user added gets uid/gid of 1000**

Add a new user – Basic method Specify only the user name.

```text
# useradd jsmith
```

Add a new user with additional Parameter You can also specify the following parameter to the useradd

* -c : Description about the user.
* -e : expiry date of the user in mm/dd/yy format

  ```text
  # adduser -c "John Smith - Oracle Developer" -e 12/31/09
  jsmith
  ```

  Verify that the user got added successfully.

  ```text
  # grep jsmith /etc/passwd
  jsmith:x:510:510:John Smith - Oracle
  Developer:/home/jsmith:/bin/bash
  ```

Change the user password

```text
# passwd jsmith
Changing password for user jsmith.
New UNIX password:
BAD PASSWORD: it is based on a dictionary word
Retype new UNIX password:
passwd: all authentication tokens updated successfully.
```

How to identify the default values used by useradd? Following are the default values that will be used when an user is created.

```text
# useradd –D
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
```

