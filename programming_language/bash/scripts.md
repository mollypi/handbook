# Scripts

```text
for i in $(ls); do echo "$i $(ls $i | wc -l)"; done
```

![](../../.gitbook/assets/Screen%20Shot%202015-08-23%20at%203.51.01%20PM.png)

![](../../.gitbook/assets/Screen%20Shot%202015-08-23%20at%204.17.57%20PM.png)

![](../../.gitbook/assets/Screen%20Shot%202015-08-23%20at%204.34.40%20PM.png)

![](../../.gitbook/assets/Screen%20Shot%202015-08-23%20at%205.10.27%20PM.png)

## List of commands you use most often:

```text
history | awk '{a[$2]++}END{for(i in a){print a[i] " " i}}' | sort -rn | head
```

## How to archive all the files that are not modified in the last x number of days?

The following command finds all the files not modified in the last 60 days under /home/jsmith directory and creates an archive files under /tmp in the format of ddmmyyyy\_archive.tar.

```text
# find /home/jsmith -type f -mtime +60 | xargs tar -cvf
/tmp/`date '+%d%m%Y'_archive.tar`
```

## Suppress standard output using &gt; /dev/null

This will be very helpful when you are debugging shell scripts, where you don’t want to display the echo statement and interested in only looking at the error messages.

```text
# cat file.txt > /dev/null
# ./shell-script.sh > /dev/null
```

## Suppress standard error using 2&gt; /dev/null

This is also helpful when you are interested in viewing only the standard output and don’t want to view the error messages.

```text
# cat invalid-file-name.txt 2> /dev/null
# ./shell-script.sh 2> /dev/null
```

Note: One of the most effective ways to use this is in the crontab, where you can suppress the output and error message of a cron task as shown below.

```text
30 1 * * * command > /dev/null 2>&1
```

## Change the Case

Convert a file to all upper-case

```text
$ tr a-z A-Z < employee.txt
100 JASON SMITH
200 JOHN DOE
300 SANJAY GUPTA
400 ASHOK SHARMA
```

1. When you are trying to delete too many files using rm, you may get error message: /bin/rm Argument list too long – Linux. Use xargs to avoid this problem.

   ```text
   find ~ -name ‘*.log’ -print0 | xargs -0 rm -f
   ```

2. Get a list of all the \*.conf file under /etc/. There are different ways to get the same result. Following example is only to demonstrate the use of xargs. The output of the find command in this example is passed to the ls –l one by one using xargs.

   ```text
   # find /etc -name "*.conf" | xargs ls –l
   ```

3. If you have a file with list of URLs that you would like to download, you can use xargs as shown below.

   ```text
   # cat url-list.txt | xargs wget –c
   ```

4. Find out all the jpg images and archive it.

   ```text
   # find / -name *.jpg -type f -print | xargs tar -cvzf
   images.tar.gz
   ```

5. Copy all the images to an external hard-drive.

   ```text
   # ls *.jpg | xargs -n1 -i cp {} /external-hard-
   drive/directory
   ```

bash script that print cpu usage,diskusage,ram usage

```text
#!/bin/bash     
echo CPU: `top -b -n1 | grep "Cpu(s)" | awk '{print $2 + $4}'` 
FREE_DATA=`free -m | grep Mem` 
CURRENT=`echo $FREE_DATA | cut -f3 -d' '`
TOTAL=`echo $FREE_DATA | cut -f2 -d' '`
echo RAM: $(echo "scale = 2; $CURRENT/$TOTAL*100" | bc)
echo HDD: `df -lh | awk '{if ($6 == "/") { print $5 }}' | head -1 | cut -d'%' -f1`
```

