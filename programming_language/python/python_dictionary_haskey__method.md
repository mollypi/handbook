# Python dictionary has\_key\(\) Method

The method has\_key\(\) returns true if a given key is available in the dictionary, otherwise it returns a false.

## Syntax

Following is the syntax for has\_key\(\) method −

```text
dict.has_key(key)
```

## Parameters

key -- This is the Key to be searched in the dictionary.

## Return Value

This method return true if a given key is available in the dictionary, otherwise it returns a false.

## Example

The following example shows the usage of has\_key\(\) method.

```text
#!/usr/bin/python

dict = {'Name': 'Zara', 'Age': 7}

print "Value : %s" %  dict.has_key('Age')
print "Value : %s" %  dict.has_key('Sex')
```

When we run above program, it produces following result −

```text
Value : True
Value : False
```

