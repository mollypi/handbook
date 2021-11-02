# Go Control structures - Go for loop, break, continue, range

The for statement is the only available looping statement in Go. The generic statement definition is:

```text
for "initialization statements"; "bool expression that has to evaluate to true"; "statements run prior to every loop except the first" {
//code to be executed if the boolean expression evaluates to true
}
```

Any of the parts of the for statement can be left out - but the semicolons have to be there \(unless all three parts are empty, in which case the semi colons can also be left out\). The best way to understand this, again, would be with a few examples.

Full program

```text
package main

import "fmt"

func main() {
    // initialize i to 0; check each time that i is less than 5; increment i for each loop after the first
    for i := 0; i < 5; i++ {
        fmt.Println("Value of i is now:", i)
    }
}
```

```text
Value of i is now: 0
Value of i is now: 1
Value of i is now: 2
Value of i is now: 3
Value of i is now: 4
```

To illustrate that you can skip parts of the statements, I’ll show you a few code snippets. All of the below are valid statements.

Partial code: for loop snippets

```text
//prints increasing value of i infinitely, since the middle check statement does not exist
for i := 0; ; i++ {
    fmt.Println("Value of i is now:", i)
}

//prints "value of i is: 0" infinitely since the incrementing part is not there and i can never reach 3
for i := 0; i < 3;  {
    fmt.Println("Value of i:", i)
}

//here both initialization and incrementing is missing in the for statement, but it is managed outside of it
s := ""
for ; s != "aaaaa";  {
    fmt.Println("Value of s:", s)
    s = s + "a"
}
```

Within the Go for loop you can use the multiple assignment to initialize many variables. Similarly to increment or update multiple variables, you will have to use the multiple assignment paradigm \(i, j = i+2, j+2\). For those used to certain other languages where you can separate statements within the for with commas, please note that this does not exist in Go.

Full program

```text
package main

import "fmt"

func main() {
    //multiple initialization; a consolidated bool expression with && and ||; multiple ‘incrementation’
    for i, j, s := 0, 5, "a"; i < 3 && j < 100 && s != "aaaaa"; i, j, s = i+1, j+1, s + "a"  {
        fmt.Println("Value of i, j, s:", i, j, s)
    }
}
```

```text
Value of i, j, s: 0 5 a
Value of i, j, s: 1 6 aa
Value of i, j, s: 2 7 aaa
```

## break keyword

The break keyword allows you to terminate a loop at that point and continue execution at the statement following the end of the for loop block. In the simplistic example below, we are making use of the break keyword to end a loop when a particular condition is met. Since the for loop has no conditions and checks as part of its declaration, it will run for ever unless we terminate the loop explicitly. Note that this is not terminating the program entirely; it is merely jumping out of the loop, and the program continues at the statement following the ending }.

```text
package main

import "fmt"

func main() {
    i := 0
    for { //since there are no checks, this is an infinite loop
        if i >= 3 { break } //break out of this for loop when this condition is met
        fmt.Println("Value of i is:", i)
        i++;
    }
    fmt.Println("A statement just after for loop.") 
}
```

```text
Value of i is: 0
Value of i is: 1
Value of i is: 2
A statement just after for loop.
```

## continue keyword

The continue keyword allows you to go back to the beginning of the for loop. Checks and increments part of the for loop are executed and therefore the next iteration of the loop is executed. This effectively means that you can use the continue statement to skip the portions of the code following the continue statement, and move on to the next iteration.

In the example below, we are making use of the continue keyword in a loop to only print odd numbers. When we encounter a number that is even \(i mod 2 is 0\), we skip printing it and go back to the beginning of the loop by executing continue.

Full program

```text
package main

import "fmt"

func main() {

    //a continue within this loop will bring back execution to the beginning of the loop.  Checks and increments in for loop will be executed.

    for i := 0; i<7 ; i++ { //control comes back here when there is a ‘continue’ within this for block
        if i%2 == 0 { 
            continue //if it is an even number, go back to beginning of for loop
        }
        fmt.Println("Odd:", i)  //execution will reach here only when i%2 is not 0, and therefore it is odd
    }
}
```

```text
Odd: 1
Odd: 3
Odd: 5
```

## range keyword

The range keyword allows you to iterate over items of a list like an array or a map. For understanding it, you could translate the range keyword to for each index of. When used with arrays and slices, it returns the integer index of the item. When used with maps, it returns the key of the next key-value pair. It works with returning either one value or two. If only one, it is the index of the item, and if it is two then it is the index and the corresponding value.

Full program

```text
package main

import "fmt"

func main() {
    //on an array, range returns the index
    a := [...]string{"a", "b", "c", "d"}
    for i := range a {
        fmt.Println("Array item", i, "is", a[i])
    }

    //on a map, range returns the key 
    capitals := map[string] string {"France":"Paris", "Italy":"Rome", "Japan":"Tokyo" }
    for key := range capitals {
        fmt.Println("Map item: Capital of", key, "is", capitals[key])
    }

    //range can also return two items, the index/key and the corresponding value 
    for key2, val := range capitals {
        fmt.Println("Map item: Capital of", key2, "is", val)
    }
}
```

```text
Array item 0 is a
Array item 1 is b
Array item 2 is c
Array item 3 is d
Map item: Capital of Japan is Tokyo
Map item: Capital of Italy is Rome
Map item: Capital of France is Paris
Map item: Capital of Japan is Tokyo
Map item: Capital of Italy is Rome
Map item: Capital of France is Paris
```

