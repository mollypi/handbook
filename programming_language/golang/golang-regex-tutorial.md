# Golang-Regex-Tutorial

## Golang-Regex-Tutorial

## Part 1: The basics \#

### Simple Matching \#\#

You want to know if a string matches a regular expression. The _MatchString_-function returns 'true' if the string-argument matches the regular expression that you prepared with _Compile_.

```text
package main

import (
    "fmt"
    "regexp"
)

func main() {
    r, err := regexp.Compile(`Hello`)

    if err != nil {
        fmt.Printf("There is a problem with your regexp.\n")
        return
    }

    // Will print 'Match'
    if r.MatchString("Hello Regular Expression.") == true {
        fmt.Printf("Match ")
    } else {
        fmt.Printf("No match ")
    }
}
```

_Compile_ is the heart of the regexp-package. Every regular expression must be prepared with _Compile_ or its sister-function _MustCompile_. The _MustCompile_-function behaves almost like _Compile_, but throws a panic if the regular expression cannot be compiled. Because any error in _MustCompile_ leads to a panic, there is no need for returning an error code as second return value. This makes it easier to chain the _MustCompile_ call with the match-function of your choice, like shown here: \(But you should avoid the repeated compilation of a regular expression in a loop for performance reasons.\)

```text
package main

import (
    "fmt"
    "regexp"
)

func main() {
    if regexp.MustCompile(`Hello`).MatchString("Hello Regular Expression.") == true {
        fmt.Printf("Match ") // Will print 'Match' again
    } else {
        fmt.Printf("No match ")
    }
}
```

The following illegal regexp

```text
    var myre = regexp.MustCompile(`\d(+`)
```

will yield

```text
panic: regexp: Compile(`\d(+`): error parsing regexp: missing argument to repetition operator: `+`

goroutine 1 [running]:
regexp.MustCompile(0x4de620, 0x4, 0x4148e8)
    go/src/pkg/regexp/regexp.go:207 +0x13f
```

The _Compile_-function returns in its second argument an error value. In this tutorial I will usually discard it, because of course all my regexes are perfect ;-\). You might get away with that if your regexps are literals, but if the regexp is derived from input at runtime you definitely want to check the error value.

For the rest of this tutorial the evaluation of the error value is skipped for brevity.

This regular expression will not match:

```text
r, err := regexp.Compile(`Hxllo`)
// Will print 'false'
fmt.Printf("%v", r.MatchString("Hello Regular Expression."))
```

### CompilePOSIX/MustCompilePOSIX \#\#

_CompilePOSIX_ and _MustCompilePOSIX_ are running a slightly different engine. The rules are implemented following the POSIX ERE \(extended regular expression\); from the viewpoint of Go this implies a _restricted_ set of rules, namely those supported by _egrep_. Thus, a couple of niceties that Go's standard re2-engine supports are not found in the POSIX version, e.g. _\A_.

```text
s := "ABCDEEEEE"
rr := regexp.MustCompile(`\AABCDE{2}|ABCDE{4}`)
rp := regexp.MustCompilePOSIX(`\AABCDE{2}|ABCDE{4}`)
fmt.Println(rr.FindAllString(s, 2))
fmt.Println(rp.FindAllString(s, 2))
```

This fails to compile, but only for the _MustCompilePOSIX_-function, because _\A_ is not part of POSIX ERE.

Furthermore the POSIX engine will prefer the _leftmost-longest_ match. It will not return after finding the first match, but will check that the found match is indeed the longest one. Thus,

```text
s := "ABCDEEEEE"
rr := regexp.MustCompile(`ABCDE{2}|ABCDE{4}`)
rp := regexp.MustCompilePOSIX(`ABCDE{2}|ABCDE{4}`)
fmt.Println(rr.FindAllString(s, 2))
fmt.Println(rp.FindAllString(s, 2))
```

will print

```text
[ABCDEE]    <- first acceptable match
[ABCDEEEE]  <- But POSIX wants the longer match
```

The two POSIX-functions are probably only the methods of choice if you have very specific requirements...

### Character classes \#\#

Character class '\w' represents any character from the class \[A-Za-z0-9\_\], mnemonic: 'word'.

```text
r, err := regexp.Compile(`H\wllo`)
// Will print 'true'. 
fmt.Printf("%v", r.MatchString("Hello Regular Expression."))
```

Character class '\d' represents any numeric digit.

```text
r, err := regexp.Compile(`\d`)
// Will print 'true':
fmt.Printf("%v", r.MatchString("Seven times seven is 49."))
// Will print 'false':
fmt.Printf("%v", r.MatchString("Seven times seven is forty-nine."))
```

Character class '\s' represents any of the following whitespaces: TAB, SPACE, CR, LF. Or more precisely \[\t\n\f\r \].

```text
r, err := regexp.Compile(`\s`)
// Will print 'true':
fmt.Printf("%v", r.MatchString("/home/bill/My Documents"))
```

Character classes can be negated by using the uppercase '\D', '\S', '\W'. Thus, '\D' is any character that is _not_ a '\d'.

```text
r, err := regexp.Compile(`\S`) // Not a whitespace
// Will print 'true', obviously there are non-whitespaces here:
fmt.Printf("%v", r.MatchString("/home/bill/My Documents"))
```

Check if a string has anything that is not a word-char.

```text
r, err := regexp.Compile(`\W`) // Not a \w character.

fmt.Printf("%v", r.MatchString("555-shoe")) // true: has a non-word char: The hyphen
fmt.Printf("%v", r.MatchString("555shoe")) // false: has no non-word char.
```

### What's in a Match? \#\#

The _FindString_-function finds a string. When you use a literal string, the result will obviously be the string itself. Only when you start using patterns and classes the result will be more interesting.

```text
r, err := regexp.Compile(`Hello`)
// Will print 'Hello'
fmt.Printf(r.FindString("Hello Regular Expression. Hullo again."))
```

When FindString does not find a string that matches the regular expression, it will return the empty string. Be aware that the empty string might also be the result of a valid match.

```text
r, err := regexp.Compile(`Hxllo`)
// Will print nothing (=the empty string)
fmt.Printf(r.FindString("Hello Regular Expression."))
```

FindString returns after the first match. If you are interested in more possible matches you would use _FindAllString\(\)_, see below.

#### Special Characters \#\#\#

The dot '.' matches any character.

```text
// Will print 'cat'.
r, err := regexp.Compile(`.at`)
fmt.Printf(r.FindString("The cat sat on the mat."))
```

'cat' was the first match.

```text
// more dot.
s:= "Nobody expects the Spanish inquisition."
//          -- --     --
r, err := regexp.Compile(`e.`)
res := r.FindAllString(s, -1) // negative: all matches
// Prints [ex ec e ]. The last item is 'e' and a space.
fmt.Printf("%v", res)
res = r.FindAllString(s, 2) // find 2 or less matches
// Prints [ex ec]. 
fmt.Printf("%v", res)
```

### Literal Special Characters \#\#

Finding one backslash '\': It must be escaped twice in the regex and once in the string.

```text
r, err := regexp.Compile("C:\\\\")
if r.MatchString("Working on drive C:\\") == true {
    fmt.Printf("Matches.") // <---
} else {
    fmt.Printf("No match.")
}
```

Finding a literal dot:

```text
r, err := regexp.Compile(`\.`)
if r.MatchString("Short.") == true {
    fmt.Printf("Has a dot.") // <---
} else {
    fmt.Printf("Has no dot.")
}
```

The other special characters that are relevant for constructing regular expressions work in a similar fashion: .+\*?\(\)\|\[\]{}^$

Finding a literal dollar symbol:

```text
r, err := regexp.Compile(`\$`)
if len(r.FindString("He paid $150 for that software.")) != 0 {
    fmt.Printf("Found $-symbol.") // <-
} else {
    fmt.Printf("No $$$.")
}
```

### Simple Repetition \#\#

The _FindAllString_-function returns an array with all the strings that matched. FindAllString takes two arguments, a string and the maximum number of matches that shall be returned. If you definitely want all matches use '-1'.

Finding words. A word is a sequence of characters of type \w. The plus symbol '+' signifies a repetition:

```text
s := "Eenie meenie miny moe."
r, err := regexp.Compile(`\w+`)
res := r.FindAllString(s, -1)
// Prints [Eenie meenie miny moe]
fmt.Printf("%v", res)
```

In contrast to wildcards used on the commandline for filename matching, the '\*' does not symbolize 'any character', but the repetition of the previous character \(or group\). While the '+' requires at least a single occurence of its preceding symbol, the '\*' is also satisfied with 0 occurences. This can lead to strange results.

```text
s := "Firstname Lastname"
r, err := regexp.Compile(`\w+\s\w+`)
res := r.FindString(s)
// Prints Firstname Lastname
fmt.Printf("%v", res)
```

But if this is some user supplied input, there might be two spaces:

```text
s := "Firstname  Lastname"
r, err := regexp.Compile(`\w+\s\w+`)
res := r.FindString(s)
// Prints nothing (the empty string=no match)
fmt.Printf("%v", res)
```

We allow any number \(but at least one\) of spaces with '\s+':

```text
s := "Firstname  Lastname"
r, err := regexp.Compile(`\w+\s+\w+`)
res := r.FindString(s)
// Prints Firstname  Lastname
fmt.Printf("%v", res)
```

If you read a text file in INI-style, you might want to be permissive regarding spaces around the equal-sign.

```text
s := "Key=Value"
r, err := regexp.Compile(`\w+=\w+`)
res := r.FindAllString(s, -1)
// OK, prints Key=Value
fmt.Printf("%v", res)
```

Now let's add some spaces around the equal sign.

```text
s := "Key = Value"
r, err := regexp.Compile(`\w+=\w+`)
res := r.FindAllString(s, -1)
// FAIL, prints nothing, the \w does not match the space.
fmt.Printf("%v", res)
```

Therefore we allow a number of spaces \(including possibly 0\) with '\s\*':

```text
s := "Key = Value"
r, err := regexp.Compile(`\w+\s*=\s*\w+`)
res := r.FindAllString(s, -1)
fmt.Printf("%v", res)
```

The Go-regexp pattern supports a few more patterns constructed with '?'.

### Anchor and Boundaries \#\#

The caret symbol ^ denotes a 'begin-of-line'.

```text
s := "Never say never."
r, err1 := regexp.Compile(`^N`)        // Do we have an 'N' at the beginning?
fmt.Printf("%v ", r.MatchString(s)) // true
t, err2 := regexp.Compile(`^n`)        // Do we have an 'n' at the beginning?
fmt.Printf("%v ", t.MatchString(s)) // false
```

The dollar symbol $ denotes an 'end-of-line'.

```text
s := "All is well that ends well"
r, err := regexp.Compile(`well$`)
fmt.Printf("%v ", r.MatchString(s)) // true

r, err = regexp.Compile(`well`)
fmt.Printf("%v ", r.MatchString(s)) // true, but matches with first
                               // occurrence of 'well'
r, err = regexp.Compile(`ends$`)
fmt.Printf("%v ", r.MatchString(s)) // false, not at end of line.
```

We saw that 'well' matched. To figure out, where exactly the regexp matched, let's have a look at the indexes. The _FindStringIndex_-function returns an array with two entries. The first entry is the index \(starting from 0, of course\) where the regular expression matched. The second is the index _in front of which_ the regexp ended.

```text
s := "All is well that ends well"
//    012345678901234567890123456
//              1         2
r, err := regexp.Compile(`well$`)
fmt.Printf("%v", r.FindStringIndex(s)) // Prints [22 26]

r, err = regexp.Compile(`well`)
fmt.Printf("%v ", r.MatchString(s)) // true, but matches with first
                          // occurrence of 'well'
fmt.Printf("%v", r.FindStringIndex(s)) // Prints [7 11], the match starts at 7 and end before 11.

r, err = regexp.Compile(`ends$`)
fmt.Printf("%v ", r.MatchString(s)) // false, not at end of line.
```

You can find a word boundary with '\b'. The _FindAllStringIndex_-function captures all the hits for a regexp in a container array.

```text
s := "How much wood would a woodchuck chuck in Hollywood?"
//    012345678901234567890123456789012345678901234567890
//              10        20        30        40        50
//             -1--         -2--                    -3--
// Find words that *start* with wood
r, err := regexp.Compile(`\bwood`)              //    1      2
fmt.Printf("%v", r.FindAllStringIndex(s, -1)) // [[9 13] [22 26]]

// Find words that *end* with wood
r, err = regexp.Compile(`wood\b`)               //   1      3 
fmt.Printf("%v", r.FindAllStringIndex(s, -1)) // [[9 13] [46 50]]

// Find words that *start* and *end* with wood
r, err = regexp.Compile(`\bwood\b`)             //   1
fmt.Printf("%v", r.FindAllStringIndex(s, -1)) // [[9 13]]
```

### Character Classes \#\#

Instead of a literal character you can require a set \(or class\) of characters at any location. In this example \[uio\] is a "character class". Any of the characters in the square brackets will satisfy the regexp. Thus, this regexp will match 'Hullo', 'Hillo', and 'Hollo'

```text
r, err := regexp.Compile(`H[uio]llo`)
// Will print 'Hullo'.
fmt.Printf(r.FindString("Hello Regular Expression. Hullo again."))
```

A negated character class reverses the match of the class. In this case it Will match all strings 'H.llo', where the dot is _not_ 'o', 'i' or 'u'. It will not match "Hullo", "Hillo", "Hollo", but it will match "Hallo" and even "H9llo".

```text
r, err := regexp.Compile(`H[^uio]llo`)
fmt.Printf("%v ", r.MatchString("Hillo")) // false
fmt.Printf("%v ", r.MatchString("Hallo")) // true
fmt.Printf("%v ", r.MatchString("H9llo")) // true
```

### POSIX character classes

The Golang regexp library implements the POSIX character classes. These are simply aliases for frequently used classes that are given are more readable name. The classes are: \([https://re2.googlecode.com/hg/doc/syntax.html](https://re2.googlecode.com/hg/doc/syntax.html)\)

```text
[:alnum:]    alphanumeric (≡ [0-9A-Za-z])
[:alpha:]    alphabetic (≡ [A-Za-z])
[:ascii:]    ASCII (≡ [\x00-\x7F])
[:blank:]    blank (≡ [\t ])
[:cntrl:]    control (≡ [\x00-\x1F\x7F])
[:digit:]    digits (≡ [0-9])
[:graph:]    graphical (≡ [!-~] == [A-Za-z0-9!"#$%&'()*+,\-./:;<=>?@[\\\]^_`{|}~])
[:lower:]    lower case (≡ [a-z])
[:print:]    printable (≡ [ -~] == [ [:graph:]])
[:punct:]    punctuation (≡ [!-/:-@[-`{-~])
[:space:]    whitespace (≡ [\t\n\v\f\r ])
[:upper:]    upper case (≡ [A-Z])
[:word:]    word characters (≡ [0-9A-Za-z_])
[:xdigit:]    hex digit (≡ [0-9A-Fa-f])
```

Note that you have to wrap an ASCII character class in \[\]. Furthmore note that whenever we speak about alphabet we are only talking about the 26 letters in ASCII range 65-90, not including letters with diacritical marks.

Example: Find a sequence of a lower case letter, a punctuation character, a space \(blank\) and a digit:

```text
r, err := regexp.Compile(`[[:lower:]][[:punct:]][[:blank:]][[:digit:]]`)
if r.MatchString("Fred: 12345769") == true {
                     ----
    fmt.Printf("Match ") // 
} else {
    fmt.Printf("No match ")
}
```

I never use those, because they require more typing, but they might actually be a good idea in projects with many developers where not everybody is as well versed in regular expressions as you are.

### Unicode Classes \#\#

Unicode is organized in blocks, typically grouped by topic or language. In this chapter I give some examples, because it's next to impossible to cover all of them \(and it doesn't really help\). Refer to [complete unicode list of the re2 engine](https://code.google.com/p/re2/wiki/Syntax).

#### Example: Greek \#\#\#

We start with a simple example from the Greek code block.

```text
r, err := regexp.Compile(`\p{Greek}`)

if r.MatchString("This is all Γςεεκ to me.") == true {
    fmt.Printf("Match ") // Will print 'Match'
} else {
     fmt.Printf("No match ")
}
```

On the Windows-1252 codepage there is a mu, but it doesn't qualify, because \p{Greek} covers only [http://en.wikipedia.org/wiki/Greek\_and\_Coptic](http://en.wikipedia.org/wiki/Greek_and_Coptic) the range U+0370..U+03FF.

```text
if r.MatchString("the µ is right before ¶") == true {
    fmt.Printf("Match ") 
} else {
     fmt.Printf("No match ") // Will print 'No match'
}
```

Some extra cool letters from the Greek and Coptic codepage that qualify as 'Greek' although they are probably Coptic, so be careful.

```text
if r.MatchString("ϵ϶ϓϔϕϖϗϘϙϚϛϜ") == true {
    fmt.Printf("Match ") // Will print 'Match'
} else {
    fmt.Printf("No match ") 
}
```

#### Example: Braille \#\#\#

You have to use a font that supports [Braille](http://en.wikipedia.org/wiki/Braille). I have my doubts that this is useful unless combined with a Braille capable printer, but there you go.

```text
r2, err := regexp.Compile(`\p{Braille}`)
if r2.MatchString("This is all ⢓⢔⢕⢖⢗⢘⢙⢚⢛ to me.") == true {
    fmt.Printf("Match ") // Will print 'Match'
} else {
    fmt.Printf("No match ")
}
```

#### Example: Cherokee \#\#\#

You have to use a font that supports Cherokee \(e.g. Code2000\). The story of the Cherokee script is definitely worth [reading about](http://en.wikipedia.org/wiki/Cherokee#Language_and_writing_system).

```text
r3, err := regexp.Compile(`\p{Cherokee}`)
if r3.MatchString("This is all ᏯᏰᏱᏲᏳᏴ to me.") == true {
    fmt.Printf("Match ") // Will print 'Match'
} else {
    fmt.Printf("No match ")
}
```

### Alternatives \#\#

You can provide alternatives using the pipe-symbol '\|' to allow two \(or more\) different possible matches. If you want to allow alternatives only in parts of the regular expression, you can use parentheses for grouping.

```text
r, err1 := regexp.Compile(`Jim|Tim`)
fmt.Printf("%v", r.MatchString("Dickie, Tom and Tim")) // true
fmt.Printf("%v", r.MatchString("Jimmy, John and Jim")) // true

t, err2 := regexp.Compile(`Santa Clara|Santa Barbara`)
s := "Clara was from Santa Barbara and Barbara was from Santa Clara"
//                   -------------                      -----------
fmt.Printf("%v", t.FindAllStringIndex(s, -1))
// [[15 28] [50 61]]

u, err3 := regexp.Compile(`Santa (Clara|Barbara)`) // Equivalent
v := "Clara was from Santa Barbara and Barbara was from Santa Clara"
//                   -------------                      -----------
fmt.Printf("%v", u.FindAllStringIndex(v, -1))
// [[15 28] [50 61]]
```

