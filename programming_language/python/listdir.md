# Listdir

## Syntax

Following is the syntax for listdir\(\) method:

```text
os.listdir(path)
```

## Parameters

path -- This is the directory, which needs to be explored.

```text
#!/usr/bin/python

import os, sys

# Open a file
path = "/var/www/html/"
dirs = os.listdir( path )

# This would print all the files and directories
for file in dirs:
   print file
```

