# Xargs

Copy all images to external hard-drive

```text
# ls *.jpg | xargs -n1 -i cp {} /external-hard-drive/directory
```

Search all jpg images in the system and archive it.

```text
# find / -name *.jpg -type f -print | xargs tar -cvzf images.tar.gz
```

Download all the URLs mentioned in the url-list.txt file

```text
# cat url-list.txt | xargs wget –c
```

```text
ps aux | grep /usr/local/xxx.py | grep -v grep | awk {' print $2'} | xargs -I {} kill {}
```

