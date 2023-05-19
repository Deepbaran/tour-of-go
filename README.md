# Tour of Go
This repository holds all the interesting and intriguing stuff that I feel, I should be taking note of, from the [Tour of Go](https://go.dev/tour/list) tutorial of golang.

---
---
## Basics 
### A. Packages, Variables and functions
### 1. Exported names
   1. In Go, a name is exported if it begins with a capital letter. For exampe, "Pi" in "math" package is an exported name byt "pi" is not.
   2. When importing a package, you can refer only to its exported names.
   3. Any "unexported" names are not accessible from outside the package.
   ```
   package main

    import (
        "fmt"
        "math"
    )

    func main() {
        fmt.Println(math.Pi) //Will run successfully
        
        //fmt.Println(math.pi) //Will give error
    }
   ```
### 2. Function arguments with shared type
   1. When two or more consecutive named function parameters share a type, you can omit the type from all but the last.
   ```
   package main

    import "fmt"

    func add(x, y int, z string) (int, string) {
        return x + y, z
    }

    func main() {
        fmt.Println(add(42, 13, "test")) //55 test
    }
   ```
### 3. Named return values
   1. Go's return values may be named. If so, they are treated as variables defined at the top of the function.
   2. These names should be used to document the meaning of the return values.
   3. A return statement without arguments returns the named return values. This is known as a "naked" return.
   4. Naked return statements should be used only in short functions. They can harm readability in longer functions.
   ```
    package main

    import "fmt"

    func split(sum int) (x, y, z int) {
        x = sum * 4 / 9
        y = sum - x
        z = 5
        return
    }

    func main() {
        fmt.Println(split(17))
    }
   ```
### 4. variable Declaration
   1. The var statement declares a list of variables; as in function argument lists, the type is last.
   2. A var statement can be at package or function level. We see both in this example.
   ```
   package main

    import "fmt"

    var c, python, java bool

    func main() {
        var i int
        fmt.Println(i, c, python, java)
    }
   ```
   - With Initializers:
     - A var declaration can include initializers, one per variable.
     - If an initializer is present, the type can be omitted; the variable will take the type of the initializer.
        ```
        package main

        import "fmt"

        var i, j int = 1, 2

        func main() {
            var c, python, java, a = true, false, "no!", 5
            fmt.Println(i, j, c, python, java, a)
        }
        ```
### 5. Basic Types:
   ```
    bool

    string

    int  int8  int16  int32  int64
    uint uint8 uint16 uint32 uint64 uintptr

    byte // alias for uint8

    rune // alias for int32
        // represents a Unicode code point

    float32 float64

    complex64 complex128
   ```
   - The int, uint, and uintptr types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems. When you need an integer value you should use int unless you have a specific reason to use a sized or unsigned integer type
### 6. Type conversions
   - The expression T(v) converts the value v to the type T.
        ```
        var i int = 42
        var f float64 = float64(i)
        var u uint = uint(f)
        ```
        ```
        i := 42
        f := float64(i)
        u := uint(f)
        ```
    - Unlike in C, in Go assignment between items of different type requires an explicit conversion.
### 7. Constants
   1. Constants are declared like variables, but with the const keyword.
   2. Constants can be character, string, boolean, or numeric values.
   3. Constants cannot be declared using the := syntax.
      > const name = "Deep"
### 8. Numeric Constants
   1. Numeric constants are high-precision values.
   2. An untyped constant takes the type needed by its context.
   3. An int can store at maximum a 64-bit integer, and sometimes less.
### 9. Declare multiple variables or multiple constsnts
   1. Variables:
        ```
        var a, b, c, d int
        ```
        ```
        //Type will be infered from the assignment values
        var a, b, c, d int = 1, 2, 3, 5;
        x, y, z := 1, true, "hello";
        ```
        ```
        var (
            ToBe   bool       = false
            MaxInt uint64     = 1<<64 - 1
            z      complex128 = cmplx.Sqrt(-5 + 12i)
        )
        ```
   2. Constants:
        ```
        const (
            ToBe   bool       = false
            MaxInt uint64     = 1<<64 - 1
            z      complex128 = cmplx.Sqrt(-5 + 12i)
        )
        ```
---
### B. Flow Control statements: for, if, else, switch and defer
### 1. For
  - Go has only one looping construct, the for loop.
  - The normal for loop
      ```
      for i := 0; i < 10; i++ {
          sum += i
      }
      ```
      ```
      sum := 1
      for ; sum < 10; {
          sum += sum
      }
      ```
  - The Go version of "while" loop.
      ```
      sum := 1
  	for sum < 1000 {
          sum += sum
      }
      ```
  - Infinite Loop
      ```
      for {
      }   
      ```
### 2. If-else
  - Go's if statements are like its for loops; the expression need not be surrounded by parentheses ( ) but the braces { } are required.
     ```
     if x < 0 && x != 0 {
		return x
     }
     ```
  - Like for, the if statement can start with a short statement to execute before the condition Variables declared by the statement are only in scope until the end of the if. They are also availale in the else block too.
    ```
    if v := math.Pow(x, n); v < lim {
		return v
	} else {
        return v-1
    }
    ```
### 3. switch
  - Go's switch is like the one in C, C++, Java, JavaScript, and PHP, except that Go only runs the selected case, not all the cases that follow. In effect, the break statement that is needed at the end of each case in those languages is provided automatically in Go. Another important difference is that Go's switch cases need not be constants, and the values involved need not be integers.
    ```
    switch os := runtime.GOOS; os {
        case "darwin":
            fmt.Println("OS X.")
        case "linux":
            fmt.Println("Linux.")
        default:
            fmt.Printf("%s.\n", os)
    }
    ```
  - Switch without a condition is the same as switch true. This construct can be a clean way to write long if-then-else chains.
    ```
    t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
    ```
### 4. Defer
  - A defer statement defers the execution of a function until the surrounding function returns. The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.
    ```
    func main() {
    	defer fmt.Println("world")

        fmt.Println("hello")
    }

    //hello
    //world
    ```
  - Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.
    ```
    func main() {
        fmt.Println("counting")

        for i := 0; i < 5; i++ {
            defer fmt.Println(i)
        }

        fmt.Println("done")
    }

    //counting
    //done
    //5
    //4
    //3
    //2
    //1
    ```
  - More on Defer - [Link](https://go.dev/blog/defer-panic-and-recover)
---
### C. More types: structs, slices, and maps
  - Struct fields can be accessed through a struct pointer.
    ```
    type Vertex struct {
        X int
        Y int
    }

    func main() {
        v := Vertex{1, 2}
        p := &v
        p.X = 1e9
        fmt.Println(v)
    }
    ```
  - The zero value of a slice is nil. A nil slice has a length and capacity of 0 and has no underlying array.
    ```
    func main() {
        var s []int
        fmt.Println(s, len(s), cap(s))
        if s == nil {
            fmt.Println("nil!")
        }
    }
    ```
  - Creating a slice with make
    > a := make([]int, 5)
  - Functions are values too. They can be passed around just like other values. Function values may be used as function arguments and return values.
    - Go functions may be closures. A closure is a function value that references variables from outside its body (The scope of it's outer function is called **Lexical scope** and both the inner and outer function will be pointing towards the same memory address and not a copy. So, if even one of them updates it, the other will get affected). The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.
    - Lexical scope refers to the ability of a function scope to access variables from the parent scope.
  - We can use literas too to initialize Slices, Structs and Maps.
---
---
## Methods and Interfaces
  ### 1. Methods
  - A method is a function with a special _receiver_ argument. The receiver appears in its own argument list between the func keyword and the method name.
  - You can only declare a method with a receiver whose type is defined in the same package as the method. You cannot declare a method with a receiver whose type is defined in another package (which includes the built-in types such as int).
  - There are basically two types of methods depending on top of receivers.
    - Value receiver -> A copy of the value is sent to the method
    - Pointer receiver -> The memory address of the real value is sent to the method
  - **Methods and pointer indirection** => Functions that take a value argument must take a value of that specific type. While methods with pointer receivers take either a value or a pointer as the receiver when they are called.
  - All methods on a given type should have either value or pointer receivers, but not a mixture of both.
  ### 2. Interfaces
  - An interface type is defined as a set of method signatures.
  - A value of interface type can hold any value that implements those methods.
  - A type implements an interface implicitly by implementing its methods. There is no explicit declaration of intent, no "implements" keyword.
  - The empty interface: The interface type that specifies zero methods is known as the empty interface.
    - > interface{}
    - An empty interface may hold values of any type. (Every type implements at least zero methods.)
    - Empty interfaces are used by code that handles values of unknown type. For example, fmt.Print takes any number of arguments of type interface{}.
---
---
## Generics
### 1. Type Parameters
  - Go functions can be written to work on multiple types using type parameters. The type parameters of a function appear between brackets, before the function's arguments.
    - > func Index[T comparable](s []T, x T) int
    - This declaration means that s is a slice of any type T that fulfills the built-in constraint comparable. x is also a value of the same type.
    - comparable is a useful constraint that makes it possible to use the == and != operators on values of the type. In this example, we use it to compare a value to all slice elements until a match is found. This Index function works for any type that supports comparison.
        ```
        package main

        import "fmt"

        // Index returns the index of x in s, or -1 if not found.
        func Index[T comparable](s []T, x T) int {
            for i, v := range s {
                // v and x are type T, which has the comparable
                // constraint, so we can use == here.
                if v == x {
                    return i
                }
            }
            return -1
        }

        func main() {
            // Index works on a slice of ints
            si := []int{10, 20, 15, -10}
            fmt.Println(Index(si, 15))

            // Index also works on a slice of strings
            ss := []string{"foo", "bar", "baz"}
            fmt.Println(Index(ss, "hello"))
        }
        ```
### 2. Generic types
  - In addition to generic functions, Go also supports generic types. A type can be parameterized with a type parameter, which could be useful for implementing generic data structures.
  - This example demonstrates a simple type declaration for a singly-linked list holding any type of value.
    ```
    package main
    import "fmt"
    // List represents a singly-linked list that holds
    // values of any type.
    type List[T any] struct {
        next *List[T]
        val  T
    }

    func main() {
        list1 := List[int]{next: nil, val: 1}
        list2 := List[int]{next: &list1, val: 2}
        list3 := List[int]{next: &list2, val: 3}
        fmt.Println(list3)
        fmt.Println(*list3.next)
        fmt.Println(*list3.next.next)
    }

    ```
---
---
## Concurrency
### Goroutines
  - Goroutines run in the same address space, so access to shared memory must be synchronized. The sync package provides useful primitives, although you won't need them much in Go as there are other primitives.
### Channels
  - Channels are a typed conduit through which you can send and receive values with the channel operator, <-. By default, sends and receives block until the other side is ready. This allows goroutines to synchronize without explicit locks or condition variables.
### Buffered Channels
  - Channels can be buffered. Provide the buffer length as the second argument to make to initialize a buffered channel:
    - > ch := make(chan int, 100)
    - Sends to a buffered channel block only when the buffer is full. Receives block when the buffer is empty.
        ```
        package main

        import "fmt"

        func main() {
            ch := make(chan int, 2)
            ch <- 1
            ch <- 2
            fmt.Println(<-ch)
            fmt.Println(<-ch)
        }
        //1
        //2
        ```
### Range and Close
  - A sender can close a channel to indicate that no more values will be sent. Receivers can test whether a channel has been closed by assigning a second parameter to the receive expression: after
    - > v, ok := <-ch
    - ok is false if there are no more values to receive and the channel is closed.
        ```
        package main

        import (
            "fmt"
        )

        func fibonacci(n int, c chan int) {
            x, y := 0, 1
            for i := 0; i < n; i++ {
                c <- x
                x, y = y, x+y
            }
            close(c)
        }

        func main() {
            c := make(chan int, 10)
            go fibonacci(cap(c), c)
            for i := range c {
                fmt.Println(i)
            }
        }
        //0
        //1
        //1
        //2
        //3
        //5
        //8
        //13
        //21
        //34
        ```
      - The loop for i := range c receives values from the channel repeatedly until it is closed.
    - Only the sender should close a channel, never the receiver. Sending on a closed channel will cause a panic.
    - Channels aren't like files; you don't usually need to close them. Closing is only necessary when the receiver must be told there are no more values coming, such as to terminate a range loop.
### Select
  - The select statement lets a goroutine wait on multiple communication operations.
  - A select blocks until one of its cases can run, then it executes that case. It chooses one at random if multiple are ready.
    ```
    package main

    import "fmt"

    func fibonacci(c, quit chan int) {
        x, y := 0, 1
        for {
            select {
            case c <- x:
                x, y = y, x+y
            case <-quit:
                fmt.Println("quit")
                return
            }
        }
    }

    func main() {
        c := make(chan int)
        quit := make(chan int)
        go func() {
            for i := 0; i < 10; i++ {
                fmt.Println(<-c)
            }
            quit <- 0
        }()
        fibonacci(c, quit)
    }
    ```
  - The default case in a select is run if no other case is ready. Use a default case to try a send or receive without blocking:
    ```
    select {
    case i := <-c:
        // use i
    default:
        // receiving from c would block
    }
    ```
  - Think of select as switch case of goroutines and channels.
### stnc.Mutex
  - We've seen how channels are great for communication among goroutines.
  - But what if we don't need communication? What if we just want to make sure only one goroutine can access a variable at a time to avoid conflicts?
  - This concept is called mutual exclusion, and the conventional name for the data structure that provides it is mutex.
  - Go's standard library provides mutual exclusion with sync.Mutex and its two methods:
    - Lock
    - Unlock
  - We can define a block of code to be executed in mutual exclusion by surrounding it with a call to Lock and Unlock as shown on the Inc method (code provided below).
  - We can also use defer to ensure the mutex will be unlocked as in the Value method (code provided below).
    ```
    package main

    import (
        "fmt"
        "sync"
        "time"
    )

    // SafeCounter is safe to use concurrently.
    type SafeCounter struct {
        mu sync.Mutex
        v  map[string]int
    }

    // Inc increments the counter for the given key.
    func (c *SafeCounter) Inc(key string) {
        c.mu.Lock()
        // Lock so only one goroutine at a time can access the map c.v.
        c.v[key]++
        c.mu.Unlock()
    }

    // Value returns the current value of the counter for the given key.
    func (c *SafeCounter) Value(key string) int {
        c.mu.Lock()
        // Lock so only one goroutine at a time can access the map c.v.
        defer c.mu.Unlock()
        return c.v[key]
    }

    func main() {
        c := SafeCounter{v: make(map[string]int)}
        for i := 0; i < 1000; i++ {
            go c.Inc("somekey")
        }

        time.Sleep(time.Second)
        fmt.Println(c.Value("somekey"))
    }
    ```
---
---