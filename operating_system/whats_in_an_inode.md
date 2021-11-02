# What's in an inode?

All UNIX files have its description stored in a structure called ‘inode’. The inode contains info about the file-size, its location, time of last access, time of last modification, permission and so on.

1. Type \(directory, link, file, b=block or c=character\), 

   name, 

2. atime=last accessed time \(ls -lu\), 
3. mtime=last modified time \(ls -l\) 
4. ctime=time the file's inode was last changed \(ls -lc\), 
5. pointers to where it is on disk \(or target if it's a link\), 
6. permission bits.

Note the ctime is the time the inode was last changed. It is NOT the creation time.

For example,  
**A chmod or a chown command will change the ctime only.  
A touch command changes all 3 times to now, but  
A touch -t command to some time in the past, changes both the mtime & atime to the past time, but ctime is set to now.  
A cat command changes just the atime to now. Note in this case, the inode does change, but this change doesn't count as far as the ctime goes.  
A &gt; command, changes the ctime & mtime, but not the atime, which is curious. Evidently, atime is the last time the file was read, not written.**

Following command will be used to show inodes of file and folders:

```text
ls -i
```

find out the inode number using ‘ls -il’ command then run below command

```text
find . -inum inode_number -exec rm -i {} \;
```

1. Inode 0 is used as a NULL value to indicate that there is no inode.
2. Inode 1 is used to keep track of any bad blocks on the disk; it is essentially a hidden file containing the bad blocks. Those bad blocks which are recorded using`e2fsck -c`.
3. Inode 2 is used by the root directory, and indicates starting of filesystem inodes.

