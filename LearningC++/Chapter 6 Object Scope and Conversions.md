## Chapter 6 Object Scope and Conversions

A **compound statement** (also called a **block**, or **block statement**) is a group of *zero or more statements* that is treated by the compiler as if it were a single statement.

If an identifier inside a namespace is used and no scope resolution is provided, the compiler will first try to find a matching declaration in that same namespace. If no matching identifier is found, the compiler will then check each containing namespace in sequence to see if a match is found, with the global namespace being checked last.

It’s legal to declare namespace blocks in multiple locations (either across multiple files, or multiple places within the same file). All declarations within the namespace are considered part of the namespace.

Namespaces can be nested inside other namespaces.

```C++
#include <iostream> namespace foo{    namespace goo // goo is a namespace inside the foo namespace    {        int add(int x, int y)        {            return x + y;        }    }} int main(){    std::cout << foo::goo::add(1, 2) << '\n';    return 0;}
```

Since C++17, nested namespaces can also be declared this way:

```C++
#include <iostream>
 
namespace foo::goo // goo is a namespace inside the foo namespace (C++17 style)
{
  int add(int x, int y)
  {
    return x + y;
  }
}
 
int main()
{
    std::cout << foo::goo::add(1, 2) << '\n';
    return 0;
}
```

**Namespace aliases**：`namespace active = foo::goo; // active now refers to foo::goo`

When you write a library or code that you want to distribute to others, always place your code inside a namespace. The code your library is used in may not follow best practices -- in such a case, if your library’s declarations aren’t in a namespace, there’s an elevated chance for naming conflicts to occur. As an additional advantage, placing library code inside a namespace also allows the user to see the contents of your library by using their editor’s auto-complete and suggestion feature.

An identifier’s `scope` determines where an identifier can be accessed within the source code. When an identifier can be accessed, we say it is `in scope`. When an identifier can not be accessed, we say it is `out of scope`. Scope is a compile-time property, and trying to use an identifier when it is out of scope will result in a compile error.

Local variables have **block scope**, which means they are *in scope* from their point of definition to the end of the block they are defined within.

Variable names must be unique within a given scope, otherwise any reference to the name will be ambiguous.

A variable’s **storage duration** (usually just called **duration**) determines what rules govern when and how a variable will be created and destroyed. In most cases, a variable’s storage duration directly determines its `lifetime`. For this reason, local variables are sometimes called **automatic variables**.

Identifiers have another property named `linkage`. An identifier’s **linkage** determines whether other declarations of that name refer to the same object or not.

Local variables have `no linkage`, which means that each declaration refers to a unique object.

**Scope** and **linkage** may seem somewhat similar. However, scope defines where a single declaration can be seen and used. Linkage defines whether multiple declarations refer to the same object or not.

In C++, variables can also be declared *outside* of a function. Such variables are called **global variables**. By convention, many developers prefix global variable identifiers with “g” or “g_” to indicate that they are global.

Global variables have **file scope** (also informally called **global scope** or **global namespace scope**), which means they are visible from the point of declaration until the end of the *file* in which they are declared. Once declared, a global variable can be used anywhere in the file from that point onward!

Because they are defined outside of a function, global variables are considered to be part of the global namespace (hence the term “global namespace scope”).

Global variables are created when the program starts, and destroyed when it ends. This is called **static duration**. Variables with *static duration* are sometimes called **static variables**.

Unlike local variables, which are uninitialized by default, static variables are zero-initialized by default.

Each block defines its own scope region. So what happens when we have a variable inside a nested block that has the same name as a variable in an outer block? When this happens, the nested variable “hides” the outer variable in areas where they are both in scope. This is called **name hiding** or **shadowing**.

However, because global variables are part of the global namespace, we can use the scope operator (::) with no prefix to tell the compiler we mean the global variable instead of the local variable.



### 6.6 Internal linkage

An identifier's linkage determines whether other declaration of that name refer to the same object or not. Local variables have no *linkage*.

Global variables and functions identifiers can have either **internal linkage** or **external linkage**.

An identifier with **internal linkage** can be seen and used within a single file, but it is not accessible from other files (that is, it is not exposed to the linker). This means that if two files have identically named identifiers with internal linkage, those identifiers will be treated as independent.

#### Global variables with internal linkage

Global variables with internal linkage are sometimes called **internal variables**. To make a non-constant global variable internal, we use the `static` keyword.

```c++
static int g_x; // non-constant globals have external linkage by default, but can be given internal linkage via the static keyword
 
const int g_y { 1 }; // const globals have internal linkage by default
constexpr int g_z { 2 }; // constexpr globals have internal linkage by default
 
int main()
{
    return 0;
}
```

`const` and `constexpr` global variables have internal linkage by default (and thus don’t need the `static` keyword -- if it is used, it will be ignored).

Here’s an example of multiple files using internal variables:

`a.cpp`

```C++
constexpr int g_x { 2 }; // this internal g_x is only accessible within a.cpp
```

`main.cpp`

```C++
#include <iostream>
 
static int g_x { 3 }; // this separate internal g_x is only accessible within main.cpp
 
int main()
{
    std::cout << g_x << '\n'; // uses main.cpp's g_x, prints 3
 
    return 0;
}
```

------

**For advanced readers**

The use of the `static` keyword above is an example of a **storage class specifier**, which sets both the name’s linkage and its storage duration (but not its scope). The most commonly used `storage class specifier` values are `static`, `extern`, and `mutable`. The term `storage class specifier` is mostly used in technical documentations.

------

#### Functions with internal linkage

Because linkage is a property of an identifier (not of a variable), function identifiers have the same linkage property that variable identifiers do. Functions default to external linkage (which we’ll cover in the next lesson), but can be set to internal linkage via the `static` keyword:

`add.cpp`

```c++
// This function is declared as static, and can now be used only within this file
// Attempts to access it from another file via a function forward declaration will fail
static int add(int x, int y)
{
    return x + y;
}
```

`main.cpp`

```c++
#include <iostream>
 
int add(int x, int y); // forward declaration for function add
 
int main()
{
    std::cout << add(3, 4) << '\n';
 
    return 0;
}
```

#### Quick Summary

```c++
// Internal global variables definitions:
static int g_x;          // defines non-initialized internal global variable (zero initialized by default)
static int g_x{ 1 };     // defines initialized internal global variable
 
const int g_y { 2 };     // defines initialized internal global const variable
constexpr int g_y { 3 }; // defines initialized internal global constexpr variable
 
// Internal function definitions:
static int foo() {};     // defines internal function
```

### 6.7 External linkage

An identifier with **external linkage** can be seen and used both from the file in which it is defined, and from other code files (via a forward declaration). In this sense, identifiers with external linkage are truly “global” in that they can be used anywhere in your program!

#### Functions have external linkage by default

In order to call a function defined in another file, you must place a `forward declaration` for the function in any other files wishing to use the function. The forward declaration tells the compiler about the existence of the function, and the linker connects the function calls to the actual function definition.

Here’s an example:

`a.cpp`

```c++
#include <iostream>
 
void sayHi() // this function has external linkage, and can be seen by other files
{
    std::cout << "Hi!";
}
```

`main.cpp`

```c++
void sayHi(); // forward declaration for function sayHi, makes sayHi accessible in this file
 
int main()
{
    sayHi(); // call to function defined in another file, linker will connect this call to the function definition
 
    return 0;
}
```

The above program prints:

```c++
Hi!
```

In the above example, the forward declaration of function `sayHi()` in `main.cpp` allows `main.cpp` to access the `sayHi()` function defined in `a.cpp`. The forward declaration satisfies the compiler, and the linker is able to link the function call to the function definition.

If function `sayHi()` had internal linkage instead, the linker would not be able to connect the function call to the function definition, and a linker error would result.

#### Global variables with external linkage

Global variables with external linkage are sometimes called **external variables**. To make a global variable external (and thus accessible by other files), we can use the `extern` keyword to do so:

```c++
int g_x { 2 }; // non-constant globals are external by default
 
extern const int g_y { 3 }; // const globals can defined as extern, making them external
extern constexpr int g_z { 3 }; // constexpr globals can defined as extern, making them external (but this is useless, see the note in the next section)
 
int main()
{
    return 0;
}
```

Non-`const` global variables are external by default (if used, the `extern` keyword will be ignored).

Note that the `extern` keyword has different meanings in different contexts. In some contexts, `extern` means “give this variable external linkage”. In other contexts, `extern` means “this is a forward declaration for an external variable that is defined somewhere else”. Yes, this is confusing.

**Warning**

---

If you want to define an uninitialized non-`const` global variable, do not use the `extern` keyword, otherwise C++ will think you're trying to make a forward declaration for the variable.

------

**Warning**

Although `constexpr` variables can be given external linkage via the `extern` keyword, they can not be forward declared, so there is no value in giving them external linkage.

Note that function forward declarations don’t need the `extern` keyword -- the compiler is able to tell whether you’re defining a new function or making a forward declaration based on whether you supply a function body or not. Variables forward declarations *do* need the `extern` keyword to help differentiate variables definitions from variable forward declarations (they look otherwise identical):

```C++
// non-constant 
int g_x; // variable definition (can have initializer if desired)
extern int g_x; // forward declaration (no initializer)
 
// constant
extern const int g_y { 1 }; // variable definition (const requires initializers)
extern const int g_y; // forward declaration (no initializer)
```

The terms “file scope” and “global scope” tend to cause confusion, and this is partly due to the way they are informally used. Technically, in C++, *all* global variables have “file scope”, and the linkage property controls whether they can be used in other files or not.

However, informally, the term “file scope” is more often applied to global variables with internal linkage, and “global scope” to global variables with external linkage (since they can be used across the whole program, with the appropriate forward declarations).

**Question 1** What’s the difference between a variable’s scope, duration, and linkage? What kind of scope, duration, and linkage do global variables have?

Scope determines where a variable is accessible. Duration determines where a variable is created and destroyed. Linkage determines whether the variable can be exported to another file or not.

Global variables have global scope (aka. file scope), which means they can be accessed from the point of declaration to the end of the file in which they are declared.

Global variables have static duration, which means they are created when the program is started, and destroyed when it ends.

Global variables can have either internal or external linkage, via the static and extern keywords respectively.

### 6.8 Why (non const)global variables are evil

If you were to ask a veteran programmer for *one* piece of advice on good programming practices, after some thought, the most likely answer would be, “Avoid global variables!”. And with good reason: global variables are one of the most historically abused concepts in the language. Although they may seem harmless in small academic programs, they are often problematic in larger ones. Many developers believe non-const global variables should be avoided completely!

But before we go into why, we should make a clarification. When developers tell you that global variables are evil, they’re usually not talking about *all* global variables. They’re mostly talking about non-const global variables.

By far the biggest reason non-const global variables are dangerous is because their values can be changed by *any* function that is called, and there is no easy way for the programmer to know that this will happen. 

One of the key reasons to declare local variables as close to where they are used as possible is because doing so minimizes the amount of code you need to look through to understand what the variable does. Global variables are at the opposite end of the spectrum -- because they can be accessed anywhere, you might have to look through the entire program to understand their usage. In small programs, this might not be an issue. In large ones, it will be.

Global variables also make your program less modular and less flexible. A function that utilizes nothing but its parameters and has no side effects is perfectly modular. Modularity helps both in understanding what a program does, as well as with reusability. Global variables reduce modularity significantly.

Initialization of static variables (which includes global variables) happens as part of program startup, before execution of the `main` function. This proceeds in two phases.

The first phase is called `static initialization`. In the static initialization phase, global variables with constexpr initializers (including literals) are initialized to those values. Also, global variables without initializers are zero-initialized.

The second phase is called `dynamic initialization`. This phase is more complex and nuanced, but the gist of it is that global variables with non-constexpr initializers are initialized.

As a rule of thumb, any use of a global variable should meet at least the following two criteria: There should only ever be one of the thing the variable represents in your program, and its use should be ubiquitous throughout your program.

**Protecting yourself from global destruction**

If you do find a good use for a non-const global variable, a few useful bits of advice will minimize the amount of trouble you can get into. This advice isn’t only for non-const global variables, but can help with all global variables.

First, prefix all non-namespaced global variables with “g” or “g_”, or better yet, put them in a namespace, to reduce the chance of naming collisions.

Second, instead of allowing direct access to the global variable, it’s a better practice to “encapsulate” the variable. Make sure the variable can only be accessed from within the file it’s declared in, eg. by making the variable static or const, then provide external global “access functions” to work with the variable. These functions can ensure proper usage is maintained (e.g. do input validation, range checking, etc…). Also, if you ever decide to change the underlying implementation (e.g. move from one database to another), you only have to update the access functions instead of every piece of code that uses the global variable directly.

Third, when writing an otherwise standalone function that uses the global variable, don’t use the variable directly in your function body. Pass it in as an argument instead. That way, if your function ever needs to use a different value for some circumstance, you can simply vary the argument. This helps maintain modularity.

### 6.9 Sharing global Constants Across Multiple Files(using inline variables)

In some applications, certain symbolic constants may need to be used throughout your code (not just in one location). These can include physics or mathematical constants that don’t change (e.g. pi or Avogadro’s number), or application-specific “tuning” values (e.g. friction or gravity coefficients). Instead of redefining these constants in every file that needs them (a violation of the “Don’t Repeat Yourself” rule), it’s better to declare them once in a central location and use them wherever needed. That way, if you ever need to change them, you only need to change them in one place, and those changes can be propagated out.

Prior to C++17, the following is the easiest and most common solution:

\(1) Create a header file to hold these constants
\(2) Inside this header file, define a namespace 
\(3) Add all your constants inside the namespace (make sure they’re *constexpr*)
\(4) #include the header file wherever you need it

**As an aside...**: The term “optimizing away” refers to any process where the compiler optimizes the performance of your program by removing things in a way that doesn’t affect the output of your program. For example, lets say you have some const variable `x` that’s initialized to value `4`. Wherever your code references variable `x`, the compiler can just replace `x` with `4` (since `x` is const, we know it won’t ever change to a different value) and avoid having to create and initialize a variable altogether.

The above method has a few potential downsides.

While this is simple (and fine for smaller programs), every time constants.h gets #included into a different code file, each of these variables is copied into the including code file. Therefore, if constants.h gets included into 20 different code files, each of these variables is duplicated 20 times. Header guards won’t stop this from happening, as they only prevent a header from being included more than once into a single including file, not from being included one time into multiple different code files. This introduces two challenges:
\(1) Changing a single constant value would require recompiling every file that includes the constants header, which can lead to lengthy rebuild times for larger projects.
\(2) If the constants are large in size and can’t be optimized away, this can use a lot of memory.

One way to avoid these problems is by turning these constants into external variables, since we can then have a single variable (initialized once) that is shared across all files. In this method, we’ll define the constants in a .cpp file (to ensure the definitions only exist in one place), and put forward declarations in the header (which will be included by other files).

---

**Note**: We use const instead of constexpr in this method because constexpr variables can’t be forward declared, even if they have external linkage. This is because the compiler needs to know the value of the variable at compile time, and a forward declaration does not provide this information.







C++17 introduced a new concept called `inline variables`. In C++, the term `inline` has evolved to mean “multiple definitions are allowed”. Thus, an **inline variable** is one that is allowed to be defined in multiple files without violating the one definition rule. Inline global variables have external linkage by default.

### 6.10 Static Local Variables

The term `static` is one of the most confusing terms in the C++ language, in large part because `static` has different meanings in different contexts.

In prior lessons, we covered that global variables have `static duration`, which means they are created when the program starts and destroyed when the program ends.

We also discussed how the `static` keyword gives a global identifier `internal linkage`, which means the identifier can only be used in the file in which it is defined.

You learned that local variables have `automatic duration` by default, which means they are created at the point of definition, and destroyed when the block is exited.

Using the `static` keyword on a local variable changes its duration from `automatic duration` to `static duration`. This means the variable is now created at the start of the program, and destroyed at the end of the program (just like a global variable). As a result, the static variable will retain its value even after it goes out of scope!

Static local variables that are zero initialized or have a constexpr initializer can be initialized at program start. Static local variables with non-constexpr initializers are initialized the first time the variable definition is encountered (the definition is skipped on subsequent calls, so no reinitialization happens). Because `s_value` has constexpr initializer `1`, `s_value` will be initialized at program start.

Static local variables can be made const. One good use for a const static local variable is when you have a function that needs to use a const value, but creating or initializing the object is expensive (e.g. you need to read the value from a database). If you used a normal local variable, the variable would be created and initialized every time the function was executed. With a const static local variable, you can create and initialize the expensive object once, and then reuse it whenever the function is called.



































### 6.11 Scope, duration, and linkage summary

#### scope summary

An identifier's scope determines where the identifier can be accessed within the source code.

- Variables with **block scope/local scope** can only be accessed within the block in which they are declared(including nested blocks). This includes:
    + Local variables
    + Function parameters
    + User-defined type definitions(such as enums and classes) declared inside a block
- Variables and functions with **global scope / file scope** can be accessed anywhere **in the file**. This includes:
    + Global variables
    + Functions
    + User-defined type definitions(such as enums and classes) declared inside a namespace or in the global scope

#### Duration summary

A variable's duration determines when it is created and destroyed.

- Variables with **automatic duration** are created at the point of definition, and destroyed when the block they are part of is exited. This includes:
    + Local variables
    + Function parameters

+ Variables with **static duration** are created when the program begins and destroyed when the program ends. This includes:
    + Global variables
    + Static local variables

+ + Variables with **dynamic duration** are created and destroyed by programmer request. This includes:

+ - - Dynamically allocated variables

#### Linkage summary

An identifier's *linkage* determines whether multiple instance of an identifier refer to the same identifier or not.

+ An identifier with **no linkage** means the identifier only refers to itself. This includes:
    + Local variables
    + User-defined type definitions (such as enums and classes) declared inside a block
+ An identifier with **internal linkage** can be accessed anywhere within the file it is declared. This includes:
    + Static global variables (initialized or uninitialized)
    + Static functions
    + Const global variables
    + Functions declared inside an unnamed namespace
    + User-defined type definitions (such as enums and classes) declared inside an unnamed namespace

+ An identifier with **external linkage** can be accessed anywhere within the file it is declared, or other files (via a forward declaration). This includes:
    + Functions
    + Non-const global variables (initialized or uninitialized)
    + Extern const global variables
    + Inline const global variables
    + User-defined type definitions (such as enums and classes) declared inside a namespace or in the global scope

Identifiers with external linkage will generally cause a duplicate definition linker error if the definitions are compiled into more than one .cpp file (due to violating the one-definition rule). There are some exceptions to this rule (for types, templates, and inline functions and variables) -- we’ll cover these further in future lessons when we talk about those topics.

Also note that functions have external linkage by default. They can be made internal by using the static keyword.

#### Forward declaration summary

You can use a forward declaration to access a function or variable in another file:

|                       Type                       |          Example          |                       Notes                       |
| :----------------------------------------------: | :-----------------------: | :-----------------------------------------------: |
|           Function forward declaration           |     void foo(int x);      |         Prototype only, no function body          |
| Non-constant global variable forward declaration |      extern int g_x;      |               Must be uninitialized               |
|    Const global variable forward declaration     |   extern const int g_x;   |               Must be uninitialized               |
|  Constexpr global variable forward declaration   | extern constexpr int g_x; | Not allowed, constexpr cannot be forward declared |

#### What the heck is a storage class specifier?

When used as part of an identifier declaration, the `static` and `extern` keywords are called **storage class specifiers**. In this context, they set the storage duration and linkage of the identifier.

C++ supports 4 active storage class specifiers:

### 6.12 Using Statements





### 6.13 Typedefs and type aliases

**Typedefs** allow the programmer to create an alias for a data type, and use the aliased name instead of the actual type name. Typedef literally stands for, “type definition”.

To declare a typedef, simply use the `typedef` keyword, followed by the type to alias, followed by the alias name:

```C++
typedef double distance_t; // define distance_t as an alias for type double
 
// The following two statements are equivalent:
double howFar;
distance_t howFar;
```



