# Linux date and Unix timstamp conversion

Current time as a Unix timestamp:

```text
date +%s
```

Output is as follows:

```text
1361542433
```

Convert a Unix timestamp to a specified date:

```text
date -d '2013-2-22 22:14' +%s
```

Output is as follows:

```text
1361542440
```

Convert Unix timestamp to a date and time Do not specify a date and time format:

```text
date -d @1361542596
```

Output is as follows:

```text
Fri Feb 22 22:16:36 CST 2013
```

Specify the conversion date format:

```text
date -d @1361542596 +"%Y-%m-%d %H:%M:%S"
```

Output is as follows:

```text
2013-02-22 22:16:36
```

