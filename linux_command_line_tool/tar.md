# TAR

The following command creates a single archive backup file called my\_home\_directory.tar under /tmp. This archive will contain all the files and subdirectories under /home/jsmith.

* Option c, stands for create an archive.
* Option v stands for verbose mode, displays additional

  information while executing the command.

* Option f indicates the archive file name mentioned in the command.

  ```text
  # tar cvf /tmp/my_home_directory.tar /home/jsmith
  ```

How do I view all the files inside the tar archive? Option t will display all the files from the tar archive.

```text
# tar tvf /tmp/my_home_directory.tar
```

How do I extract all the files from a tar archive? Option x will extract the files from the tar archive as shown below. This will extract the content to the current directory location from where the command is executed.

```text
# tar xvf /tmp/my_home_directory.tar
```

How do I extract tar.gz files to a specific directory?

```text
# tar xvfz /tmp/my_home_directory.tar.gz –C /home/ramesh
```

How to use gzip with tar? Add option z to the tar command when dealing with tar.gz compressed file.

```text
# tar cvfz /tmp/my_home_directory.tar.gz /home/jsmith
# tar xvfz /tmp/my_home_directory.tar.gz
# tar tvfz /tmp/my_home_directory.tar.gz
```

Note: Using gzip is faster when compared to bzip2.

How to use bzip2 with tar? Add option j to the tar command when dealing with tar.bz2 compressed file.

```text
# tar cvfj /tmp/my_home_directory.tar.bz2 /home/jsmith
# tar xvfj /tmp/my_home_directory.tar.bz2
# tar tvfj /tmp/my_home_directory.tar.bz2
```

Note: Using bizp2 gives higher level of compression when compared to gzip.

## tar command examples

Create a new tar archive.

```text
$ tar cvf archive_name.tar dirname/
```

Extract from an existing tar archive.

```text
$ tar xvf archive_name.tar
```

View an existing tar archive.

```text
$ tar tvf archive_name.tar
```

