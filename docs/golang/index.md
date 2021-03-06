### **Go**

### How to Write Go Code

* [How to Write Go Code](https://golang.org/doc/code.html)

### Basic constructs and elementary data types

#### Filenames, Keywords and Identifiers

* **Filenames** consist of lowercase-letters (that may separated by underscores _), like `scanner.go` and `scanner_test.go`.  Filenames may not contain spaces or any other special characters.
* **Identifiers** begin with a letter (a **letter** is every letter in Unicode UTF-8 or _) and followed by 0 or more letters or Unicode digits, like: `X56`, `group1`, `_x23`, `i`, `өԑ12`.
    * The `_` itself is a special identifier, called the **blank identifier**. It can be used in declarations or variable assignments like any other identifier (and any type can be assigned to it), but its value is discarded, so it cannot be used anymore in the code that follows.
* **Keywords** or reserved words are: `break`, `default`, `func`, `interface`, `select`, `case`, `defer`, `go`, `map`, `struct`, `chan`, `else`, `goto`, `package`, `switch`, `const`, `fallthrough`, `if`, `range`, `type`, `continue`, `for`, `import`, `return`, `var`.
* **Predeclared identifiers** (names of elementary types and some basic built-in functions): `append`, `bool`, `byte`, `cap`, `close`, `complex`, `complex64`, `complex128`, `uint16`, `copy`, `false`, `float32`, `float64`, `imag`, `int`, `int8`, `int16`, `uint32`, `int32`, `int64`, `iota`, `len`, `make`, `new`, `nil`, `panic`, `uint64`, `print`, `println`, `real`, `recover`, `string`, `true`, `uint`, `uint8`, `uintptr`,
* Programs consist out of keywords, constants, variables, operators, types and functions.
* Delimiters: parentheses `( )`, brackets `[ ]` and braces `{ }`.
* Punctuation characters: `.`, `;`, `...`.
* Code is structured in **statements**. A statement doesn’t need to end with a `;`. The Go compiler automatically inserts semicolons at the end of statements. However, if multiple statements are written on one line (which is not encouraged for readability reasons), they must be separated by `;`.

#### Packages, import and visibility

[hello_world.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_4/hello_world.go)

Every go file belongs to one and only one **package** (like a library or namespace in other languages). Many different .go files can belong to one package, so the filename(s) and package name are generally not the same.

* The package to which the code-file belongs must be indicated on the first line, e.g. `package main`.
* A standalone executable belongs to package `main` and each Go application contains one package `main`.
* A package name is written in lowercase letters.

An application can consist of different packages. Even if you use only package `main`, you don’t have to stuff all code in one big file: you can make a number of smaller files each having `package main` as the first codeline. If you compile a source file with a package name other than `main`, like `pack1`, the object file is stored in `pack1.a`.

The [standard library](https://golang.org/pkg/#stdlib) contains ready-to-use packages of the Go installation.

##### **Package compilation**

* To build a program, the packages, and the files within them, must be compiled in the correct order.  Package dependencies determine the order in which to build packages.
* Within a package, the source files must all be compiled together. The package is compiled as a unit, and by convention each directory contains one package.
* If a package is changed and recompiled, all the client programs that use this package must be recompiled.
* The package model uses **explicit dependencies** to enable faster builds. [TWTG p52]

##### **Import**

A Go program is created by linking together a set of packages through the `import` keyword. For example, `import "fmt"` tells Go that this program needs (functions, or other elements, from) the package `fmt`.

* The package names are enclosed within ""
* Import loads the public declarations from the compiled package; it does not insert the source code.

If multiple packages are needed, they can each be imported by a separate statement:

```go
import "fmt"
import "os"
```

The shorter and more elegant way (called *factoring the keyword*, also applicable to `const`, `var` and `type`) is available (<u>it is also clearer to list the package names in alphabetical order</u>):

```go
import (
	"fmt"
	"os"
)
```

* If the name of a package does not start with . or /, like "fmt" or "container/list", Go looks for it in the global Go tree.
* If it starts with ./ the package is searched in the actual directory
* If it starts with /, the package is searched for in the (absolute) path indicated.

Apart from `_`, identifiers of code-objects have to be unique in a package: there can be no naming conflicts. But the same identifier can be used in different packages: the package name qualifies it to be different.

##### **Visibility rule**

Packages expose their code-objects to code outside of the package according to the following rule:

* When the identifier (of a constant, variable, type, function, struct field, etc.) starts with an uppercase letter, like `Group1`, then the "object" with this identifier is visible in code outside the package (thus available to client-programs, "importers" of the package), it is said to be exported (like public in OO languages).
* Identifiers which start with a lowercase letter are not visible outside the package, but they are visible and usable in the whole package (like private).

Some notes on this rule:

* Uppercase letters can come from the entire Unicode-range, like Greek; not only ASCII letters are allowed.
* Importing a package gives (only) access to the exported objects in that package.
* Packages also serve as namespaces and can help to avoid name-clashes (name-conflicts): variables with the same name in two packages are differentiated by their package name. For example, `pack1.Thing` and `pack2.Thing`.

A package can also be given another name (an **alias**), for example:

```go
package main

import fm "fmt"  // alias

func main() {
	fm.Println("hello, world")
}
```

Importing a package which is not used in the rest of the code is a build-error.

##### **Package level declarations and initializations**

After the import statement, zero or more constants (`const`), variables (`var)`, and types (`type`) can be declared; these are *global* (have package scope) and are known in all functions in the code (like `c` and `v` in [gotemplate.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_4/gotemplate.go) below), and they are followed by one or more functions (`func`).

* [gotemplate.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_4/gotemplate.go)

#### Functions

The simplest function declaration has the format:

```go
func functionName()
```

A `main` function as starting is required. The `main` function must have no arguments and no return values results.

When the program executes, after initializations the first function called (the entry-point of the application) will be `main.main()`. The program exits immediately and successfully when `main.main` returns.

The code in functions (the body) is enclosed between braces: `{ }`.

* The first `{` must be on the same line as the function declaration: this is imposed by the compiler and `gofmt`.
* The last `}` is positioned after the function code beneath function.
* For small functions it is allowed that everything is written on one line, like for example: `func Sum(a, b int) int { return a + b }`.
* The same rule applies wherever `{ }` are used (for example: `if`, etc.).

Schematically, a general function looks like:

```go
func functionName(param1 type1, param2 type2, ...) (ret1 type1, ret2 type2, ...) {
	// ...
}
```

##### **Function names**

* `PascalCasing` (exported): function names only start with a capital letter when the function has to be used outside the package.
* `camelCasing` (private): every new word in the name starts with a capital letter.

##### **`fmt.Print` and `fmt.Println`**

* The line `fmt.Println("hello, world")` calls the function `Println` from the package `fmt`, which prints the string-parameter to the console, followed by a newline-character `\n`.
* The same result can be obtained with `fmt.Print("hello, world\n")`.
* `Print` and `Println` can also be applied to variables, like in: `fmt.Println(arr)`; they use the default output-format for the variable `arr`.

##### **`print` and `println`**

Printing a string or a variable can be done even simpler with the predefined functions `print` and `println`. For example,

```go
print("ABC")
println("ABC")
println(i)
```

These are only to be used in the debugging phase; when deploying a program replace them with their `fmt` relatives.

#### Comments

Comments are not compiled. They are used by [godoc](https://godoc.org/golang.org/x/tools/cmd/godoc).

* A one-line comment starts with `//`, at the beginning or somewhere in a line; this is mostly used.
* A multi-line or block-comment starts with `/*` and ends with `*/`, nesting is not allowed; this is used for making package documentation and commenting out code.

[TWTG p56]

Every package should have a package comment, a block comment immediately preceding the package statement. A package can be spread over many files, but the comment needs to be in only one of them. This comment is shown when a developer demands info of the package with `godoc`.

#### Types

A declaration of a variable with `var` automatically initializes it to the zero-value defined for its type. A type defines the set of values and the set of operations that can take place on those values.

Types can be:

* **Elementary** (or **primitive**): int, float, bool, string,
* **Structured** (or **composite**): struct, array, slice, map, channel,
* **Interfaces** only describe the behavior of a type.

A structured type which has no real value (yet) has the value `nil`, which is also the default value for these types (in C anc C++ it is NULL).

Use the keyword `type` for defining your own type (usually a struct type). It is also possible to define an **alias** for an existing type, for example:

```go
type (
	IZ int
	FZ float
	STR string
)
```

#### Program structure

[gotemplate.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_4/gotemplate.go) is an example of the general structure of a Go program. This structure is not necessary, the compiler does not mind if `main()` or the variable declarations come last, but a uniform structure makes Go code better readable from top to bottom:

* After import: declare constants, variables and the types
* Then comes the `init()` function if there is any: this is a special function that every package can contain and that is executed first.
* Then comes the `main()` function (only in the package `main`)
* Then come the rest of the functions:
    * The methods on the types first, or;
    * The functions in order as they are called from `main()` onwards, or;
    * The methods and functions alphabetically if the number of functions is high.

##### **Order of execution**

1. All packages in package `main` are imported in the order as indicated.
2. In every package, if it imports packages, Step 1 is called for this package (recursively) but a certain package is imported only once.
3. For every package (in reverse order of dependencies) all constants and variables are evaluated, and the `init()` if it contains this function.
4. In package `main` the same happens, and then `main()` starts executing.

#### Conversions

A value can be *converted* (*cast*, *coerced*) into a value of another type. Go never does *implicit* (automatic) conversion, it must be done explicitly with the syntax like a function call, as in `valueOfTypeB = typeB(valueOfTypeA)`. For example:

```go
a := 5.0
b := int(a)
```

This can only succeed in certain well defined cases (from a narrower type to a broader type, for example: `int16` to `int32`). When converting from a broader type to a narrower type (for example: `int32` to `int16`, or `float32` to `int`) loss of value (*truncation*) can occur. When the conversion is impossible and the compiler detects this, a compile-error is given, otherwise a runtime-error occurs.

<u>Variables with the same underlying type can be converted into one another:</u>

```go
var a IZ = 5
c := int(a)
d := IZ(c)
```

#### Naming

[TWTG p60]

* [Names - Effective Go](https://golang.org/doc/effective_go.html#names)

#### Constants

A constant (`const`) contains data which does not change. This data can only be of type boolean, number (integer, float or complex) or string. It is defined with the format `const identifier [type] = value` (type specifier `[type]` is optional, the compiler can implicitly derive the type from the value). For example:

```go
const b string = "abc"
const Pi = 3.14159
```

A value derived from an untyped constant becomes typed when it is used within a context that requires a typed value. For example:

```go
var n int
f(n + 5) // untyped numeric constant 5 becomes typed as int
```

Constants must be evaluated at compile time; a const can be defined as a calculation, but all the values necessary for the calculation must be available at compile time. For example:

```go
const c1 = 2/3 // ok
const c2 = getNumber() // gives the build error: getNumber() used as value
```

Constants can be used for **enumerations**:

```go
const (
	Unknown = 0
	Female = 1
	Male = 2
)
```

In such cases, the value `iota` can be used to enumerate the values:

```go
const (  // iota is reset to 0
	c0 = iota  // c0 == 0
	c1 = iota  // c1 == 1
	c2 = iota  // c2 == 2
)
```

This can be shortened to:

```go
const (
	c0 = iota
	c1
	c2
)
```

[TWTG p62-63]

See [Constant declarations](https://golang.org/ref/spec#Constant_declarations) and [iota](https://golang.org/ref/spec#Iota).

#### Variables

The general form for declaring a variable is `var identifier type`. The type is written after the identifier of the variable, contrary to almost any other programming language. Why did the Go designers chose for this convention? It removes some ambiguity which can exist in C declarations, for example, in `int* a, b;`, only `a` is a pointer and `b` is not; in Go, they can both be declared pointers as follows: `var a, b *int`.

Variables can be declared using the following format:

```go
var a int
var b bool
var str string
```

This can be also written as (<u>mainly used to declare variables globally</u>):

```go
var (
	a int
	b bool
	str string
)
```

When a variable is declared it contains automatically the default [zero value](https://golang.org/ref/spec#The_zero_value) for its type: `false` for booleans, 0 for integers, 0.0 for floats, "" for strings, and `nil` for pointers, functions, interfaces, slices, channels, and maps. <u>All memory in Go is initialized.</u>

A variable (constant, type, function) is only known in a certain range of the program, called the **scope**:

* Variables declared outside of any function (at the top level) have **global scope** (or **package scope**): they are visible and available in all source files of the package.
* Variables declared in a function have **local scope**: they are only known in that function, the same goes for parameters and return-variables.
* Variables defined inside such a construct (e.g. `if`, `for`) are only known within that construct (**construct scope**). Mostly you can think of a scope as the codeblock ( surrounded by `{ }` ) in which the variable is declared.

Although identifiers have to be unique, an identifier declared in a block may be redeclared in an inner block: in this block (but only there) the redeclared variable takes priority and *shadows* the outer variable with the same name; if used, care must be taken to avoid subtle errors.

Variables can get their value (which is called *assigning* and uses the assignment operator `=`) at compile time, but a value can also be computed or changed during runtime. For example:

```go
a = 15
b = false
```

In general, `a` variable `b` can only be assigned to a variable `a` as in `a = b`, when `a` and `b` are of the same type.

Declaration and assignment (initialization) can be combined, in the general format `var identifier [type] = value`. For example:

```go
var a int = 15
var b bool = false
```

However, the Go compiler is intelligent enough to derive the type of a variable from its value (dynamically, also called **automatic type inference**, similar to Python and Ruby, but there it happens in run time), so the following forms (omitting the type) are also correct:

```go
var a = 15
var b = false
// Or, equivalently:
var (
	a = 15
	b = false
)
```

It can still be useful to include the type information in the case where you want the variable to be typed something different than what would be inferred, such as in: `var n int64 = 2`.

However, an expression (declaration) like `var a` is not correct, because the compiler has no clue about the type of `a`.

Variables could also be expressions computed at runtime, like:

```go
var (
	HOME = os.Getenv("HOME")
	USER = os.Getenv("USER")
)
```

The `var` syntax is mainly used at a global, package level; in functions it is replaced by the short declaration syntax `:=`.

#### Value types and reference types

Memory in a computer is used in programs as a enormous number of **words**:

* All words have the same length of 32 bits (4 bytes) or 64 bits (8 bytes), according to the processor and the operating system.
* All words are identified by their memory address (represented as a hexadecimal number).

All variables of elementary (primitive) types like int, float, bool, string are **value types**. They point directly to their value contained in memory.

Composite types like arrays and structs are also value types.

When assigning the value of a value type to another variable: `j = i`, a copy of the original value `i` is made in memory, as illustrated in the figure below:

[![Fig 4.2: Assignment of value types](twtg_figure_4.2.png)](twtg_figure_4.2.png "Fig 4.2: Assignment of value types")

The memory address of the word where variable `i` is stored is given by `&i`.

A **reference type** variable `r1` contains the address of the memory location where the value of `r1` is stored (or at least the first word of it). This address, called a **pointer**, is also contained in a word.

The different words a reference type points to could be sequential memory addresses (the memory layout is said to be contiguously) which is the most efficient storage for computation, or the words could be spread around, each pointing to the next.

When assigning `r2 = r1`, only the reference (the address) is copied, as illustrated in the figure below:

[![Fig 4.3: Reference types and assignment](twtg_figure_4.3.png)](twtg_figure_4.3.png "Fig 4.3: Reference types and assignment")

If the value of `r1` is modified, all references of that value (like `r1` and `r2`) then point to the modified content.

In Go, pointers are reference types, as well as slices, maps and channels. The variables that are referenced are stored in the heap, which is garbage collected and which is a much larger memory space than the stack.

#### Printing

* The function `fmt.Printf` is visible outside the `fmt` package because it starts with a `P`, and is used to print output to the console. It generally uses a format string as its first argument.
    * This format string can contain one or more format-specifiers `%..`, where `..` denotes the type of the value to be inserted, e.g. `%s` stands for a string value, `%v` is the general default format specifier.
* The function `fmt.Sprintf` behaves in exactly the same way as Printf, but simply returns the formatted string.
* The functions `fmt.Print` and `fmt.Println` perform fully automatic formatting of their arguments using the format-specifier `%v`, adding spaces between arguments and the latter a newline at the end. For example:
    * `fmt.Print("Hello:", 23)` produces as output: `Hello: 23`.

#### Short forms of declaration and assignment

##### **Initializing declaration with `:=`**

With the type omitted, the keyword `var` is pretty superfluous (e.g. `var a = 50`), so it may be written as `a: = 50`, and the types of is inferred by the compiler.

`a := 50` is the preferred form, but <u>it can only be used inside functions, not in package scope.</u> The `:=` operator effectively makes a new variable; it is also called an **initializing declaration**.

If after the lines above in the same codeblock we declare `a := 20`, this is not allowed: the compiler gives the error "no new variables on left side of `:=`"; however `a = 20` is ok because then the same variable only gets a new value.

##### **Undeclared and unused variables**

* A variable a which is used, but not declared, gives a compiler error: "undefined: a".
* Declaring a *local* variable, but not using it, is a compiler error: "a declared and not used". However, for global variables, this is allowed. [TWTG p69]

##### **Multiple declaration and assignment**

* Multiple declarations of variables of the same type on a single line, like: `var a, b, c int`. This is an important reason why the type is written after the identifier(s).
* Multiple assignments of variables (parallel or simultaneous assignment):
    * For already-declared variables: `a, b, c = 5, 7, "abc"`
    * For undeclared variables: `a, b, c := 5, 7, "abc"`

With two variables it can be used to perform a *swap* of the values: `a, b = b, a`.

The blank identifier `_` can also be used to throw away values, like the value 5 in: `_, b = 5, 7`

`_` is in effect a write-only variable, you cannot ask for its value. <u>It exists because a declared variable in Go must also be used, and sometimes you don’t need to use all return values from a function.</u>

The multiple assignment is also used when a function returns more than 1 value, for example: `val, err = func1(var1)`.

### Structs
#### Visibility

The naming of the struct type and its fields adheres to the visibility rule. It is possible that an exported struct type has a mix of fields: some exported, others not.

#### Factory methods

Force using factory methods on a private type [TWTG p233]:

```go
wrong := new(matrix.matrix)    // will NOT compile (matrix is private)
right := matrix.NewMatrix(...)   // the ONLY way to instantiate a matrix
```

#### Structs with tags

Only the package `reflect` can access tag content. `reflect.TypeOf()` on a variable gives the right type; if this is a struct type, it can be indexed by `Field`, and then the `Tag` property can be used. For example:

* [struct_tag.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_10/struct_tag.go)

#### Anonymous fields and embedded structs

Conflicting names [TWTG p239]

1. An outer name hides an inner name. This provides a way to override a field or method.
2. If the same name appears twice at the same level, it is an error if the name is used by the program.

### Methods

* Receiver type
* Method set: collection of all the methods on a given type `T` (or `*T`)
* No method overloading
* A method and the type on which it acts must be defined in the same package
* Pointer or value as receiver: if for a type `T` a method `Meth()` exists on `*T` and `t` is a variable of type `T`, then `t.Meth()` is automatically translated to `(&t).Meth()` [TWTG p246]

#### Methods on embedded types and inheritance

* Overriding: [method4.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_10/method4.go) [TWTG p250]
* Embedding multiple anonymous types: [mult_inheritance.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_10/mult_inheritance.go) [TWTG p253-254]

#### Embed functionality in a type

1. Aggregation (or composition): include a named field of the type of the wanted functionality, [embed_func1.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_10/embed_func1.go)
2. Embedding: [embed_func2.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_10/embed_func2.go)

#### Format specifiers

`String()`-method on a type [TWTG p259]:

* `%T`: complete type specification
* `%#v` complete output of the instance with its fields

### Interfaces

Interfaces in Go provide a way to specify the behavior of an object: if something can do this, then it can be used here.

* A type doesn’t have to state explicitly that it implements an interface: interfaces are satisfied implicitly. Multiple types can implement the same interface.
* A type that implements an interface can also have other functions.
* A type can implement many interfaces.
* An interface type can contain a reference to an instance of any of the types that implement the interface (an interface has what is called a dynamic type)

The interface variable both contains the value of the receiver instance and a pointer to the appropriate method in a method table.

* [interfaces_poly.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/interfaces_poly.go)

#### Interface embedding interfaces
An interface can contain the name of one or more other interface(s), which is equivalent to explicitly enumerating the methods of the embedded interface in the containing interface. [TWTG p270]

#### Detect and convert the type of an interface variable: type assertions

We can test if `varI` (interface variable) contains at a certain moment a variable of type `T` with the type assertion test [TWTG p271]:

```go
if v, ok := varI.(T); ok {
	// checked type assertion
}
```

* [type_interfaces.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/type_interfaces.go)

#### The type switch

* [Type switch](https://golang.org/doc/effective_go.html#type_switch)

<small>[type_switch.go](https://gist.github.com/shichao-an/36c167f2b8f649e4a468)</small>

<script src="https://gist.github.com/shichao-an/36c167f2b8f649e4a468.js"></script>

#### Testing if a value implements an interface

`v` is a value and we want to test whether it implements the `Stringer` interface:

```go
if sv, ok := v.(Stringer); ok {
	fmt.Printf("v implements String(): %s\n", sv.String()); // note: sv, not v
}
```

Writing functions so that they accept an interface variable as a parameter makes them more general. Use interfaces to make code more generally applicable.

#### Variables of interface type

A variable of interface type stores a pair: the concrete value assigned to the variable, and that value's type descriptor.

* [The representation of an interface](http://blog.golang.org/laws-of-reflection#TOC_3.)

#### Using method sets with interfaces

1. Pointer methods can be called with pointers.
2. Value methods can be called with values.
3. Value-receiver methods can be called with pointer values because they can be dereferenced first.
4. Pointer-receiver methods **cannot** be called with values, however, because the value stored inside an interface has no address.

Examples:

* [methodset2.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/methodset2.go)
* [sort.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/sort/sort.go)
* [sortmain.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/sortmain.go)

#### Empty Interface

A variable of empty interface type `interface{}` can through assignment receive a variable of any type.

#### Interface Slice

* [Interface slice](https://github.com/golang/go/wiki/InterfaceSlice)

#### Interface to interface

An interface value can also be assigned to another interface value, as long as the underlying value implements the necessary methods.

<small>[twtg_11.9.5.go](https://gist.github.com/shichao-an/4f97af9d9b7333f95b87)</small>

<script src="https://gist.github.com/shichao-an/4f97af9d9b7333f95b87.js"></script>

### Reflection

Reflection is the ability of a program to examine its own structure, particularly through the types; it’s a form of *metaprogramming*. `reflect` can be used to investigate types and variables at runtime, e.g. its size, its methods, and it can also call these methods "dynamically".

* `reflect.TypeOf`
* `reflect.ValueOf`

<small>[twtg_11.10.1.go](https://gist.github.com/shichao-an/2eae3c34f717fb5bdde8)</small>

<script src="https://gist.github.com/shichao-an/2eae3c34f717fb5bdde8.js"></script>

* `v.Kind()`: returns a constant indicating the type
* `v.Interface()`: recovers the (interface) value

Example:

* [reflect1.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/reflect1.go)

#### Setting a value through reflection

* [Settability](http://golang.org/pkg/reflect/#Value.CanSet): a `Value` can be changed only if it is addressable and was not obtained by the use of unexported struct fields, [reflect2.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/reflect2.go)

#### Reflection on structs

* [reflect_struct.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/reflect_struct.go)

#### `Printf` and reflection

`Printf` uses the reflection package to unpack it and discover the argument list, [print.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/print.go)

#### Interfaces and dynamic typing

* In Go there are no classes: data (structures, or more general types) and methods are treated *orthogonally*, they are much more <u>loosely coupled</u>.
* There is no requirement for explicitly declaring that a type satisfies an interface. This allows interfaces to be defined and used without having to modify existing code.
* Types implementing an interface can be passed to any function which takes that interface as an argument. This resembles much more the **duck typing** in dynamic languages.
* Extraction of an interface reduces thereby the number of types and methods needed [TWTG p301]: [multi_interfaces_poly.go](https://github.com/shichao-an/twtg/blob/master/code_examples/chapter_11/multi_interfaces_poly.go)
    * If a type must implement a new interface, the type itself doesn’t have to be changed, you must only make the new method(s) on the type. [TWTG p303]
* Empty interface and function overloading [TWTG p304]
* Inheritance of interfaces:
    * A type includes (embeds) another type (which implements one or more interfaces) as a pointer, then the type can use all of the interfaces-methods. [TWTG p304]
    * A type can also inherit from multiple interfaces providing something like **multiple inheritance**. [TWTG p305]

#### Summary of object-orientedness of Go

[TWTG p306]

* Encapsulation (data hiding): visibility rule
    * **Package scope**: lowercase
    * **Exported**: uppercase
* Inheritance: embedding one or multiple types
* Polymorphism: a variable of a type can be assigned to a variable of any interface it implements. Types and interfaces are loosely coupled; multiple inheritance is possible through implementing multiple interfaces.

#### Higher order functions
[TWTG p306-309]

- - -

### References

* [TWTG] *The Way To Go: A Thorough Introduction To The Go Programming Language*
* [DOC] [Documentation](https://golang.org/doc/)
* [SPEC] [The Go Programming Language Specification](https://golang.org/ref/spec#The_zero_value)
* [EG] [Effective Go](https://golang.org/doc/effective_go.html)
* [TGB] [The Go Blog](https://blog.golang.org/)
