# How-To rename the extension for a batch of files?

```text
for file in *.html; do
    mv "$file" "`basename $file .html`.txt"
done
```

```text
for file in *.htm; do 
    mv "$file" "${file%.htm}.html"; 
done
```

Bash has an extensive set of variable expansion options. The one used here, '%', removes the smallest matching suffix from the value of the variable. The pattern is a glob pattern, so ${file%._} would also work. The '%%' operator removes the largest matching suffix, and is interchangeable in the example above, as the pattern is fixed, ${file%%._}.html would turn a.b.htm into a.html though.

See the variable substition section of the bash manpage for more neat tricks. There's a lot that can be done within bash directly.

```text
for i in $(ls *.done); do 
    mv "$i" "$(basename "$i" .done)"; 
done
```

```text
$ for file in * ; do cp $file $file.bak; done
```

