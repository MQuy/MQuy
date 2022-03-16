## The Go Programming

### Tutorial

- Go code is organized into packages. A package consists of one or more `.go` source files in a single directory. Each source file begins with a `package` declaration, followed by a list of other packages that it imports, and then the declarations of the program.
- Package `main` is special, it is for a standalone executable program.
- Go does not require semicolons at the of statements or declarations expect where two or more appear on the same line. Newlines are placed matters to proper parsing of Go code.
- If `var` declaration is not explicitly initialized, it is implicitly initialized to the zero value of its type.
  ```go
  var a, b string // a and b are empty strings ""
  ```
- `:=` symbol is part of short variable declaration, declare one or more variables and give them appropriate types based on the intializier values.
- `for` loop is the only loop statement in Go. It has a number of forms
  ```go
  for initialization; condition; post { // initialization, condition and post are optional
  }
  for index, value := range xxx {
  }
  ```
- functions and other package-level entities may be declared in any order.
  ```go
  fnc demo() {
  	hello()
  }
  fnc hello() {
  }
  ```
- `const` declaration gives names to constants whose value must be a number, string or boolean.
- `switch/case` doesn't fall through from one to the next like C (but you can use `fallthrough` to override this behaviour).
  ```
  switch coinflip() {
  case "heads":
  	heads++
  case "tails":
  	tails++
  default:
  	fmt.Println("landed on edge!)
  }
  switch {				// tagless switch
  	case x > 0:
  		return 1
  	case x < 0:
  		return 0
  }
  ```
- similar to C, statements may be labled so `break` and `continue` can refer to them.

### Program Structure

#### Names & Declarations

- functions, variables, constants, types, statement labels, and packages have their name begin with a letter (includes Unicode letters) or an underscore. Go supports [subset of unicode](https://go.dev/ref/spec#Identifiers) for naming.
- if an entity is declared outside of function, it is visible in all files of its package. If the first letter is uppercase, it is exported means it is accessible outside of its package.
- predefined can be redefined while keyword cannot.
  ```go
  func delta(old, new int) int { return new - old } // ok
  func delta(old, if int) int { return if - old } // compile error
  ```

#### Variables

- there are four major kinds of declarations: `var`, `const`, `type` and `func`.
- variable declaration has its form (either type or expression part may be omitted, but not both). Without expression part, zero value of that type is assigned to a variable (`0` for numbers, `false` for boolean, `""` for strings, and `nil` for interface and reference types).
  ```go
  var name type = expression
  ----
  var i, j, k int                 // int, int int
  var b, f, s = true, 2.3, "four" // boolean, float64, string
  ```
- package-level variables are initialized before `main` begins, and local variables are initialized as their declarations are encountered during function execution.
- short variable declaration is only valid within a function ([simplier parsing](https://groups.google.com/g/golang-nuts/c/qTZemuGDV6o/m/IyCwXPJsUFIJ)).
  💡 if a variable is already declared in the same lexical block (outer block is ignored), a short variable declaration acts like an assignment.
  ```go
  in, err := os.Open(infile)
  ...
  out, err := os.Open(outfile)  // reuse err and assign it a value
  out, err := os.Open(file)     // compile error, at least one new variable required
  ```
- pointers are comparable, two pointers are equal if they point to the same variable or both are nil.
  💡 it is perfectly safe to return the address of a local variable (go performs escape analysis).
  ```go
  func f() *int {
    v := 1
    return &v
  }
  func demo() {
    fmt.Println(f() == f())     // false
  }
  ```
- `new(T)` creates an unamed variable of type `T`, initializes it to the zero value of `T` and returns its adress. There is no difference between a variable created by `new` and an ordinary ways.
  ```go
  p := new(int)   // p is a int pointer, *int
  ```
- package level variable's lifetime is the entire execution of the program, while local variables live on until they become unreachable.
- compiler may choose to allocate local variables on the heap (like example above) or stack.

#### Assignments & Type Declarations

- tupple assignments allows several variables to be assigned at once (all right-hand side expressions are evaluated before any updates to varibles).
  ```go
  x, y = 1, 2
  x, y = y, x
  ```
- type declaration has its form.
  ```go
  type name underlying-name
  ----
  type Celsius float64
  type Fahrenheit float64   // Celsius and Fahrenheit are not the same type, cannot be compared
  ```
- a conversion from one type to another is allowed if both have the same underlying type, or both are unnamed pointer types that point to variables of the same underlying type.
- comparsion like `==`, `<` can be used for values of the same type, or an unamed type with the same underlying type.
  ```go
  var c Celsius
  var f Fahrenheit
  fmt.Println(c == 0)           // true
  fmt.Println(f >= 0)           // true
  fmt.Println(c == f)           // compile error: mismatch type
  fmt.Println(c == Celsius(f))  // true
  ```

#### Packages and Files & Scope

- `.go` files in a package is sorted by name before invoking the compiler.
- if you need to initialize for variables or setup environment, [`init`](https://stackoverflow.com/a/24790378/1349340) function could be useful. `init` can be defined in each file and automatically executed when program starts.
- scope of a declaration (the part of the source code where declared name refers to that declaration) is a region of the program text (compile-time property) while lifetime of a variable is the range of time during execution when a variable can be referred (run-time property).
  ```go
  func f() *int {
    x := 1          // scope of x is only function f while lifetime of x exists in both functions f and g
    return &x
  }
  func g() {
    p := f()
    *p = 2
  }
  ```
- not all lexical blocks correspond to explicit brace-delimited sequences of statements.
  ```go
  x := "hello world"
  for i:= 0; i < len(x) ; x++ {     // creates two lexical blocks: explicit block for loop body and
    x := x[i]                       // implicit outer block for variables in initialization clause
  }
  ----
  if x := f(); x == 0 {             // if (switch) creates two blocks, one for its body and
    fmt.Println(x)                  // implicit outer block for its condition
  } else if y := g(x); x == y {     // second if statement is nested with the first -> x is accessible in the second.
    fmt.Println(x, y)
  } else {
    fmt.Println(x, y)
  }
  ```

### Basic Data Types

There are 4 categories:

- basic types: numbers, strings and booleans.
- aggregrate types: arrays and structs.
- reference types: pointers, slices, maps, functions and channels.
- interface types.

#### Integers & Float-Point Numbers & Complex Numbers & Booleans

- `rune` type is a synonym for `int32` which is used for Unicode code point.
- sign of remainder is always the same sign as the dividend.
  ```go
  -5 % 3 == -5 % -3     // -2
  ```
- overflow happens when the result of an arithmetic operation (signed and unsigned) has more bits than can be presented in the result type. The high order bits which do not fit are silently discard.
  ```go
  var u uint8 = 255;
  fmt.Println(u, u + 1, u *u)   // 255, 0, 1
  ----
  var i int8 = 127
  fmt.Println(i, i + 1, i * i)  // 127, -128, 1
  ```
- left/right shifts (unsigned numbers) and left shifts (signed numbers) fill the vacated bits with zero, while right shifts (signed numbers) fill the vacated bits with copies of the sign bit.
- why the builtin `len` function returns `int` instead of `uint` despite it never be negative. The reason is, `len` is usually used in loop and if its type is `uint` the condition `i >= 0` is always true (when i == 0, next i-- turns i to maximum integer number).
- float to integer is implement-dependent if value is out of range for the target.
  ```go
  f := 1e100    // a float64
  i := int(f)   // implement-dependent
  ```

#### Strings

- a string is an immutable sequence of bytes (can contain bytes with value 0).
- index operator `s[i]` retrieves the i-th byte of string `s` not i-th character of a string (UTF-8 encoding of a non-ASCII code point requires two or more bytes).
- string comparision operators like `==`, `<` is done byte by byte.
- string values are immutable
  ```go
  s := "left foot"
  t := s
  s += ", right foot"   // new string
  s[0] = "L"            // compile error
  ```
  ![string](https://i.imgur.com/dIX2jOX.png)
  ```go
  s := "hello, world"
  hello := s[:5]
  world := s[7:]
  ```
- Go source files always encoded in UTF-8 and text strings are interpreted as UTF-8.
  | Format | Range | |
  | ------------------------------------- | :------------: | ----: |
  | `0xxxxxxx` | runes 0-127 | ASCII |
  | `110xxxxx 10xxxxxx` | 128-2047 | |
  | `1110xxxx 10xxxxxx 10xxxxxx` | 2048-65523 | |
  | `11110xxx 10xxxxxx 10xxxxxx 10xxxxxx` | 65536-0x10ffff | |
- we need to use other mechanisms to handle individual Unicode characters.
  ![unicode](https://i.imgur.com/fLlmmWt.png)
  ```go
  import "unicode/utf8"
  s := "Hello, Image"
  fmt.Println(len(s))                    // "13"
  fmt.Println(utf8.RuneCountInString(s)) // "9"
  ```
- each time a UTF-8 decoder, explicit in a call to `utf8.DecodeRuneInString` or implicit in a range loop, consumes an unexpected input byte, it generates a special Unicode replacement character `'\uFFFD'`.
- if a slice of runes is converted to a string, it produces the concatenation of the UTF-8 encoding of each runes.
  ```go
  fmt.Println(string(65))     // "A", not "65"
  fmt.Println(string(0x4eac)) // valid unicode
  fmt.Println(string(1234567)) // invalid unicode, the replacement character is substituted
  ```
- a string contains an array of bytes, once it is created, it is immutable while elements of a byte slice can be freely modified.

#### Constants

- constants are expressions whose value is known to the compiler and whose evaluation is guaranteed to occur at compile time.
- basic constant types: boolean, string or number.
- results of all arithmetic, logical and comparsion operations applied to constant operands are constants (same for builtin functions such as `len`, `cap`, `real`, `imag`, `complex`, and `unsafe.Sizeof`).
- when a sequence of constants is declared as a group, the right-hand side expression may be omitted for all but the first of the group.
  ```go
  const (
    a = 1
    b
    c = 2
    d
  )
  fmt.Printf(a, b, c, d)    // 1, 1, 2, 2
  ```
- only constants can be untyped (delay type evaluation). When an untype constant is assigned to a variable, it is implicitly converted to the type of that varable if possible.
  ```go
  var f float64 = 3 + 0i // untyped complex -> float64
  f = 2                  // untyped integer -> float64
  f = 1e123              // untyped floating-point -> float64
  f = 'a'                // untyped rune -> float64
  ```
- untyped integers are converted to `int`, whose size is not guaranteed, but untyped floating-point and complex numbers are converted to explicitly sized type `float64` and `complex128` (language has no unsized `float` and `complex` because it is very difficult to write correct numerical algorithms without knowing the size of floating-point data types).
  ```go
  i := 0      // untyped integer;        implicit int(0)
  r := '\000' // untyped rune;           implicit rune('\000')
  f := 0.0    // untyped floating-point; implicit float64(0.0)
  c := 0i     // untyped complex;        implicit complex128(0i)
  ```

### Composite Types

#### Arrays

- an array is a fixed-length sequence of zero or more elements of a particular type.
  ```go
  var q [3]int = [3]int{1, 2, 3}
  q := [...]int{1, 2, 3}
  ```
- if an array's element type is comparable then the array type is comparable too
  ```go
  a := [2]int{1, 2}
  b := [...]int{1, 2}
  c := [2]{1, 3}
  fmt.Println(a == b, a == c, b == c) // true false false
  d := [3]{1, 2}      // {1, 2, 0}
  fmt.Println(a == d) // compile error
  ```
- function call is "pass by value", it means passing large arrays is inefficient.

#### Slices

- a slice represents a variable-length sequence whose elements all have the same type.
- slice is similar to Rust `Vec`.
  ![represent](https://i.imgur.com/7OJxKCQ.png)
- for both string and slice, using `x[m:n]` return a subsequence sharing the underlying representation of the original.
  ```go
  s := []int{0, 1, 2, 3, 4, 5}  // s is a slice
  ```
- unlike arrays, slices are not comparable (cannot use `==`).
- unless clearly documented, otherwise, Go functions should treat all zero-length slices the same way whether nil or non-nil.
  ```go
  var s []int    // len(s) == 0, s == nil
  s = nil        // len(s) == 0, s == nil
  s = []int(nil) // len(s) == 0, s == nil
  s = []int{}    // len(s) == 0, s != nil
  ```

#### Maps

- a map is a hash table.
  ```go
  ages := map[string]int {
    "alice": 31,
    "charlie": 34,
  }
  ```
- if a key is not present, lookup returns the zero value of its type.
- we cannot take a map element's address because growing a map causes rehashing of existing elements into new storage locations.
  ```go
  _ = &age["alice"]     // compile error
  ```
- the order of map iteration is unspecified. In practice, the order is random, varying from one execution to the next (force program to not rely on the order).
- like slices, most of operations on maps are safe to perform on a nil map reference (same as empty map).
  ```go
  age, ok := ages["bob"]  // ok == true if ages contains bob key, otherwise, ok == false
  ```
- unlike slices, maps cannot be compared to each other.

#### Structs

- field order is significant to type identify.
  ```go
  type X struct {
    Id int
    Name string
  }
  // !=
  type X struct {
    Name string
    Id int
  }
  ```
- there are two forms of struct literal (two forms cannot be mixed)
  ```go
  type Point struct { X, Y int }
  p := Point{1, 2}    // every field has to be in the right order.
  p := Point{X = 1}   // omitted fields are assigned to zero of their type.
  ```
- two structs are comparable if all fields are comparable.
  ```go
  type Point struct { X, Y int }
  p1 := Point{1, 2}
  p2 := Point{2, 1}
  fmt.Println(p1 == p2)   // false
  ```
- similar to C, anonymous embed struct allows us to shorthand accessing
  ```go
  type Point struct { X, Y int }
  type Circle struct { Point }
  type Wheel struct { Circle }
  var w Wheel
  w.X = 8
  ```
- the struct field's visible depends its letter-case
  - uppercase: is exported -> visible outside of the package.
  - lowercase: is not exported -> only be accessed within the same package.

#### JSON

- only visible fields are visited when encoding/decoding.
- field's name can be mapped to another name via field tags.
  ```go
  type Album {
    Year int `json:"released"`
  }
  ```
