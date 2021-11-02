# Golang

## Why Go?

```text
 “Go is like a better C, from the guys that didn’t bring you C++” — Ikai Lan
```

## Features of Go:

* Syntax and environment adopting patterns.
* Fast compilation time.
* Remote package management.
* Online package documentation.
* Built-in concurrency primitives.
* Interfaces to replace virtual inheritance.
* Type embedding to replace non-virtual inheritance.
* Compiled into statically linked native binaries without external dependencies.
* Simple language specifications.
* Large built-in library.
* Light testing framework.

## The Go Language

TYPES

## Integers

Integers are numbers without a decimal component. Unlike us, the computers use a base-2 representation called binary system. Go’s integer types are : **uint8, int8, uint16, int16, uint32, int32, uint64, int64.**

8, 16, 32 and 64 tell us how many bits each of the types use.

Also, a **byte** is an alias for **uint8** and a rune for **int32**.

There are also 3 machine dependent integer types: **uint, int and uintptr**. They are machine dependent because their size depends on the type of architecture you are using.

```text
var x int = 404
var y uint32 = 234242
```

## Floating Point Numbers

Floating Point Numbers can be defined as numbers with a decimal component or more generally known as Real Numbers.

Go has two floating point types : **float32 and float64**

Go also has two complex number types : **complex64 and complex128**

```text
var x float64 = 34.23
var y complex64 = 3 + 2i
```

## Strings

A string is a sequence of characters of a definite length for textual representation.

String literals in Go can be created using either double quotes “Hello World” or back quotes `Hello World`.

Double quotes cannot contain newlines but allow special escape sequences like \n and \t.

Strings can be concatenated using the + symbol.

The length of a string can be obtained using the len\(\) function.

Using indices, individual character of a string can be obtained.

```text
var weird string = "weird string"
a := `Hello to all`
```

## Boolean

Booleans are a special 1 bit integer type used to indicate True or False.

The logical operators which can be used on booleans are :

```text
and &&
or ||
not !
```

## Variables

Variables in Go are initialized using the var keyword and then followed by the variable name and variable type.

```text
var x int
var y string = "Hello world"
```

You can also initialize variables in a shorter way.

Here the data type is internally assigned.

```text
x := 8
y := "Hello world"
```

You can also declare constants using the assignment operator, Numeric constants in Go are high-precision values.

```text
const y = "Hello world"
const Pi = 3.14
```

## Loops and Control Structures

**For**

For loops are used just as in C and Java.

```text
for i := 0; i < 10; i++ {
    fmt.Println("The value of i : ", i)
}
```

or

```text
for i != 0 {
    fmt.Println("The value of i :", i)
}
```

The **range** keyword can be used to range over a list of values also

```text
a := [ ]int{4, 3, 1, 7, 3, 9, 2}
for _, i := range a {
    sum = sum + i
}
```

## If

If statements are also similar to those in C and Java.

```text
if num % 2 == 0 {
    fmt.Println("Even")
} else {
    fmt.Println("Odd")
}
```

## Switch

Switch statements starts with the keyword switch and follows with multiple cases.

```text
switch i {
    case 0 : fmt.Println("Zero")
    case 1 : fmt.Println("One")
    case 2 : fmt.Println("Two")
    case 3 : fmt.Println("Three")
    case 4 : fmt.Println("Four")
    case 5 : fmt.Println("Five")
    case 6 : fmt.Println("Six")
    case 7 : fmt.Println("Seven")
    case 8 : fmt.Println("Eight")
    case 9 : fmt.Println("Nine")
    default : fmt.Println("Unknown Number")
}
```

## ARRAYS, SLICES AND MAPS

## Arrays

The type \[n\]T is an array of size n of type T.

```text
var a [10]int 
x := [5]float64 {
    34,
    63,
    56,
    84,
    23,
}
```

## Slices

A slice is a segment of an array. The length of a slice is not fixed. Creation of slice is via the make command.

```text
x := make([]int, 5)
```

## Maps

A map is an unordered collection of key-value pairs. Also known as an associative array, a hash table or a dictionary, maps are used to look up a value by its associated key.

```text
var x map[string]int
x["Hello"] = 1
```

## Functions

Functions are defined using the keyword func followed by the function name and arguments and the return values.

```text
func say_hello() {
    fmt.Println("Hello From the function")
}
```

If the result parameters are named, a return statement without arguments return the current values of the variables

```text
func details(a []int) (x, y int) {
    x := len(a)
    y := cap(a)
}
```

## Multiple return types

Go supports multiple return types.

```text
func calculate(a, b int) (sum, prod int) {
    sum := a + b
    prod := a * b
    return sum, prod
}

func main() {
    a, b := calculate(3, 4)
}
```

## Variadic Function

Functions can take 0 to undefined variables of a particular data type.

```text
func total(args ...int) (sum int) {
    sum := 0
    for _, i := range args {
        sum = sum + i
    }
    return sum
}
```

## Closure

Consider the following example

```text
func main() {
dog := func() {
        fmt.Println("Woof!")
}
    dog()
}
```

Here we are actually assigning the function to a variable.

Similarly.

```text
func increm() func() int {
    x := 0
    return func() int {
        x = x + 1
        return x
    }
}

func main() {
    y := increm()
    fmt.Println(y)
    fmt.Println(y)
}

Output :
1
2
```

Here, the func increm returns a func of type func\(\) int which still refers to the variable x which is outside the body of the func. This is known as a closure func.

## Pointers

Pointers, yes pointers! Go has pointers, but no pointer arithmetic.

```text
x := 5
y := &x
fmt.Println(*y)

Output :
5
```

y points to x and the value of y is obtained using a \* operator. The & operator is used to get the address of a variable.

The new operator is used to allocate memory to a pointer variable.

```text
x := new(int)
*x = 9
fmt.Println(*x)

Output :
9
```

## STRUCTURES, METHODS AND INTERFACES

## Structures

A structure is a user defined data type which contains named fields. A structure is basically an object in Go which has data types and methods on it.

```text
type dog struct {
    breed string
    age int
    owner string
}

type cow struct {
    age int
    farm string
}

rocky := dog { "Lab", 5, "Roy" }
bigcow := cow { age : 3, farm : "Donalds" }
fmt.Println("Dogs age is : ", rocky.age )
fmt.Println("Cows age is : ", bigcow.age )
```

We declare structures using the type and struct keyword. Individual elements of the structure are accessed using the dot operator.

## Methods

Assume, we want to know what the dog/cow says, we can define a method on the given structure to perform a specific task, in our case to speak.

```text
func (d dog) speak() string {
    return "Woof"
}

func (c cow) speak() string {
    return "Moo"
}
```

With this you can now use:

```text
fmt.Println(dog.speak())
fmt.Println(cow.speak())

Output :
Woof
Moo
```

Here, the function speak takes a dog and cow types respectively and responds with a string based on the type.

## Interfaces

Interfaces are basically a collection of methods. Lets define an interface on the function speak.

```text
type Speaker interface {
    speak() string
}
```

So, any type which has a method called speak automatically satisfies the Speaker interface.

Hence we can have.

```text
var a Speaker
a :=  dog { "Lab", 5, "Roy" }
fmt.Println(a)
a := cow { age : 3, farm : "Donalds" }
fmt.Println(a)

Output :
{Lab 5 Roy}
{3 Donalds}
```

In Go interfaces are implicitly satisfied, this one of the best features of the language.

Let us take an example, the Println function takes an interface which holds a method of type String\(\) string

Defining our own method for the dog type we can print the data as per what is most suitable to us.

```text
func (d dog) String() string {
    return fmt.Sprintf("The dog is %s, it is currently %d years old and belongs to %s", d.breed, d.age, d.owner)
}
b :=dog { "Lab", 5, "Roy" }
fmt.Println(b)

Output :
The dog is Lab, It is 5 years old and belongs to Roy
```

Because the dog has a String function and the interface taken by Println requires a String function, we satisfy that interface.

## Concurrency

Go has a rich support for concurrency via goroutines and channels. This is one of the highlights of the language.

## Goroutines

Goroutines can be described as functions which run concurrently from other functions. They’re invoked following the go keyword. As the function is invoked it runs concurrently and the rest of the code is executed without waiting for the completion of that function. The main function is implicitly a goroutine.

```text
func foo(n int) {
    for i := 0; i < n; i = i + 2; {
        fmt.Printf("%d : %d ", n, i)
        time.Sleep(time.Milliseconds * 50)
        }
} 

func main() {
      for i := 0; i < 10; i++ {
            go f(i)
    }
     var input string
     fmt.Scanln(&input)
}
```

Now the function prints out all the no’s from different goroutines, as they’re concurrently run. The sleeper makes sure that a particular routine waits for 50ms before the next iteration, this gives time for the other routines to run.

## Channels

For intercommunication of concurrent functions we use channels. Channels must be created before being used, using the chan keyword. Let us create a channel of type string.

```text
var c chan string = make(chan string)
```

Lets consider a small example

```text
func sender(c chan string) {
     for {
              c <- "Hello"
     }
}
func receiver(c chan string) {
     for{
                fmt.Println(
```

Here the sender function continuously sends the string to the c channel and the receiver function prints the value received from the channel continuously.

The function prototype can also restrict the direction of data flow from and to the channel.

Let us replace the sender and the receiver function

```text
func sender(c chan<- string)
func receiver(c <-chan string)
```

There is also a select option when handling channels, this acts like a switch statement selecting the channel which is ready.

## Final words

To move ahead with Go, have a look at the official documentation and I recommend the tour.

Also go through the talks held on Go, in various Gophercons I will be attending Gopeherconf India next year.

Have a look at the way Go code is organised and also how the testing framework is used in Go. Learn to use the automatic code formatting tool gofmt and also the automatic documentation generator godoc.

