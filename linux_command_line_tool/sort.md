# SORT

![](../.gitbook/assets/Screen%20Shot%202015-08-23%20at%204.58.24%20PM.png)

Sort a file in ascending order

```text
$ sort names.txt
```

Sort a file in descending order

```text
$ sort -r names.txt
```

Sort passwd file by 3rd field.

```text
$ sort -t: -k 3n /etc/passwd | more
```

Sort a colon delimited text file on 2nd field \(employee\_id\)

```text
$ sort -t: -k 2 names.txt
Emma Thomas:100:Marketing
Alex Jason:200:Sales
Madison Randy:300:Product Development
Sanjay Gupta:400:Support
Nisha Singh:500:Sales
```

Sort a tab delimited text file on 3rd field \(department\_name\) and suppress duplicates

```text
$ sort -t: -u -k 3 names.txt
Emma Thomas:100:Marketing
Madison Randy:300:Product Development
Alex Jason:200:Sales
Sanjay Gupta:400:Support
```

Sort the passwd file by the 3rd field \(numeric userid\)

```text
$ sort -t: -k 3n /etc/passwd | more
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

