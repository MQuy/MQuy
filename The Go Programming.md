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
