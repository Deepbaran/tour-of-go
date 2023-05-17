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
