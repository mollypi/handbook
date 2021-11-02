# Dirname

dirname\(\) returns the first part of the split path:

```text
import os.path

for path in [ '/one/two/three', 
              '/one/two/three/',
              '/',
              '.',
              '']:
    print '"%s" : "%s"' % (path, os.path.dirname(path))
$ python ospath_dirname.py

"/one/two/three" : "/one/two"
"/one/two/three/" : "/one/two/three"
"/" : "/"
"." : ""
"" : ""
```

