# golang-notes
This repository holds all the interesting and intriguing stuff that I feel, I should be taking note of, from the [Tour of Go](https://go.dev/tour/list) tutorial of golang.

---
## 1. Exported names
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
## 2. Function arguments with shared type
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
## 3. Named return values
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
## 4. variable Declaration
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
## 5. Basic Types:
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
## 6. Type conversions
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
## 7. Constants
   1. Constants are declared like variables, but with the const keyword.
   2. Constants can be character, string, boolean, or numeric values.
   3. Constants cannot be declared using the := syntax.
      > const name = "Deep"
## 8. Numeric Constants
   1. Numeric constants are high-precision values.
   2. An untyped constant takes the type needed by its context.
   3. An int can store at maximum a 64-bit integer, and sometimes less.
## 9. Declare multiple variables or multiple constsnts
   1. Variables:
        ```
        var (
            ToBe   bool       = false
            MaxInt uint64     = 1<<64 - 1
            z      complex128 = cmplx.Sqrt(-5 + 12i)
        )
        ```
   2. Constants:
        ```

        ```