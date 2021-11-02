# FIND

（the "+" means "greater than", "-" means "less than"）

```text
find . -type f -print -delete
```

Find files using file-name \( case in-sensitve find\)

```text
# find -iname "MyCProgram.c"
```

Execute commands on files found by the find command

```text
$ find -iname "MyCProgram.c" -exec md5sum {} \;
```

Find all empty files in home directory

```text
# find ~ -empty
```

![](../.gitbook/assets/Screen%20Shot%202015-08-23%20at%202.55.49%20PM.png)

