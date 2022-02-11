## Chapter 2 C++ Basics

A **function** is a reusable sequence of statements designed to do a particular job. When the return statement is executed, the return value is copied from the function back to the caller. This process is called **return by value**. The return value from `main` is sometimes called a **status code** (also sometimes called an **exit code**, or rarely a **return code**), as it is used to indicate whether the program ran successfully or not.

How Parameters and Arguments work together

When a function is called, all of the parameters of the function are created as variables, and the value of each of the arguments is *copied* into the matching parameter. This process is called **pass by value**.

Note that the number of arguments must generally match the number of function parameters, or the compiler will throw an error. The argument passed to a function can be any valid expression (as the argument is essentially just an initializer for the parameter, and initializers can be any valid expression).

The C++ specification does not define whether function calls evaluate arguments left to right or right to left. Take care not to make function calls where argument order matters.

Function parameters and return values are the key mechanisms by which functions can be written in a reusable way, as it allows us to write functions that can perform tasks and return retrieved or calculated results back to the caller without knowing what the specific inputs or outputs are ahead of time.

Local Scope

Function parameters, as well as variables defined inside the function body, are called **local variables** (as opposed to global variables).

Function parameters are created and initialized when the function is entered, and variables within the function body are created and initialized at the point of definition. Local variables are destroyed in the opposite order of creation at the end of the set of curly braces in which it is defined (or for a function parameter, at the end of the function).

Much like a person’s lifetime is defined to be the time between their birth and death, an object’s **lifetime** is defined to be the time between its creation and destruction. Note that variable creation and destruction happen when the program is running (called runtime), not at compile time. Therefore, lifetime is a runtime property.

An identifier’s **scope** determines where the identifier can be accessed within the source code. When an identifier can be accessed, we say it is **in scope**. When an identifier can not be accessed, we say it is **out of scope**. Scope is a compile-time property, and trying to use an identifier when it is not in scope will result in a compile error.

A local variable’s scope begins at the point of variable definition, and stops at the end of the set of curly braces in which they are defined (or for function parameters, at the end of the function). This ensures variables can not be used before the point of definition (even if the compiler opts to create them before then).

Note that local variables have the same definitions for scope and lifetime. For local variables, scope and lifetime are linked -- that is, a variable’s lifetime starts when it enters scope, and ends when it goes out of scope.

Remember, lifetime is a runtime property, and scope is a compile-time property, so although we are talking about both in the same program, they are enforced at different points.

Why Use Functions?

Functions provide a number of benefits that make them extremely useful in programs of non-trivial length or complexity.

- Organization -- As programs grow in complexity, having all the code live inside the main() function becomes increasingly complicated. A function is almost like a mini-program that we can write separately from the main program, without having to think about the rest of the program while we write it. This allows us to reduce a complicated program into smaller, more manageable chunks, which reduces the overall complexity of our program.
- Reusability -- Once a function is written, it can be called multiple times from within the program. This avoids duplicated code (“Don’t Repeat Yourself”) and minimizes the probability of copy/paste errors. Functions can also be shared with other programs, reducing the amount of code that has to be written from scratch (and retested) each time.
- Testing -- Because functions reduce code redundancy, there’s less code to test in the first place. Also because functions are self-contained, once we’ve tested a function to ensure it works, we don’t need to test it again unless we change it. This reduces the amount of code we have to test at one time, making it much easier to find bugs (or avoid them in the first place).
- Extensibility -- When we need to extend our program to handle a case it didn’t handle before, functions allow us to make the change in one place and have that change take effect every time the function is called.
- Abstraction -- In order to use a function, you only need to know its name, inputs, outputs, and where it lives. You don’t need to know how it works, or what other code it’s dependent upon to use it. This lowers the amount of knowledge required to use other people’s code (including everything in the standard library).

How effectively using Functions?

One of the biggest challenges new programmers encounter (besides learning the language) is understanding when and how to use functions effectively. Here are a few basic guidelines for writing functions:

- Statements that appear more than once in a program should generally be made into a function. For example, if we’re reading input from the user multiple times in the same way, that’s a great candidate for a function. If we output something in the same way multiple times, that’s also a great candidate for a function.
- Code that has a well-defined set of inputs and outputs is a good candidate for a function, particularly if it is complicated. For example, if we have a list of items that we want to sort, the code to do the sorting would make a great function, even if it’s only done once. The input is the unsorted list, and the output is the sorted list.
- A function should generally perform one (and only one) task.
- When a function becomes too long, too complicated, or hard to understand, it can be split into multiple subfunctions. This is called **refactoring**.

New programmers often wonder what happens if they forward declare a function but do not define it.

The answer is: it depends. If a forward declaration is made, but the function is never called, the program will compile and run fine. However, if a forward declaration is made and the function is called, but the program never defines the function, the program will compile okay, but the linker will complain that it can’t resolve the function call.

Declarations vs. Definitions

In C++, you’ll often hear the words “declaration” and “definition” used, often interchangeably. What do they mean? You now have enough of a framework to understand the difference between the two.

A **definition** actually implements (for functions or types) or instantiates (for variables) the identifier. A definition is needed to satisfy the *linker*. If you use an identifier without providing a definition, the linker will error.

The **one definition rule** (or ODR for short) is a well-known rule in C++. The ODR has three parts:

1. Within a given *file*, a function, object, type, or template can only have one definition.
2. Within a given *program*, an object or normal function can only have one definition. This distinction is made because programs can have more than one file (we’ll cover this in the next lesson).
3. Types, templates, inline functions, and variables are allowed to have identical definitions in different files. We haven’t covered what most of these things are yet, so don’t worry about this for now -- we’ll bring it back up when it’s relevant.

Violating part 1 of the ODR will cause the compiler to issue a redefinition error. Violating ODR part 2 will likely cause the linker to issue a redefinition error. Violating ODR part 3 will cause undefined behavior.

A **declaration** is a statement that tells the *compiler* about the existence of an identifier and its type information.

A declaration is all that is needed to satisfy the compiler. This is why we can use a forward declaration to tell the compiler about an identifier that isn’t actually defined until later.

In C++, all definitions also serve as declarations. This is why *int x* appears in our examples for both definitions and declarations. Since *int x* is a definition, it’s a declaration too. In most cases, a definition serves our purposes, as it satisfies both the compiler and linker. We only need to provide an explicit declaration when we want to use an identifier before it has been defined. In C++, all definitions also serve as declarations. 

While it is true that all definitions are declarations, the converse is not true: all declarations are not definitions. An example of this is the function prototype -- it satisfies the compiler, but not the linker. These declarations that aren’t definitions are called **pure declarations**. Other types of pure declarations include forward declarations for variables and type declarations (you will encounter these in future lessons, no need to worry about them now).

Remember, the compiler compiles each file individually. It does not know about the contents of other code files, or remember anything it has seen from previously compiled code files. So even though the compiler may have seen the definition of function *add* previously (if it compiled *add.cpp* first), it doesn’t remember.

Most naming collisions occur in two cases:
\1) Two (or more) definitions for a function (or global variable) are introduced into separate files that are compiled into the same program. This will result in a **linker error**, as shown above.
\2) Two (or more) definitions for a function (or global variable) are introduced into the same file (often via an #include). This will result in a **compiler error**.

Namespace

As programs get larger and use more identifiers, the odds of a naming collision being introduced increases significantly. The good news is that C++ provides plenty of mechanisms for avoiding naming collisions. Local scope, which keeps local variables defined inside functions from conflicting with each other, is one such mechanism. But local scope doesn’t work for function names. So how do we keep function names from conflicting with each other?

A **namespace** is a region that allows you to declare names inside of it for the purpose of disambiguation. The namespace provides a scope (called **namespace scope**) to the names declared inside of it -- which simply means that any name declared inside the namespace won’t be mistaken for identical names in other scopes.

Within a namespace, all names must be unique, otherwise a naming collision will result.

In C++, any name that is not defined inside a class, function, or a namespace is considered to be part of an implicitly defined namespace called the **global namespace** (sometimes also called **the global scope**).

It turns out that *std::cout*‘s name isn’t really *std::cout*. It’s actually just *cout*, and *std* is the name of the namespace that identifier *cout* is part of. Because *cout* is defined in the *std* namespace, the name *cout* won’t conflict with any objects or functions named *cout* that we create in the global namespace.

Similarly, when accessing an identifier that is defined in a namespace (e.g. *std::cout*) , you need to tell the compiler that we’re looking for an identifier defined inside the namespace (*std*).

The :: symbol is an operator called the **scope resolution operator**. The identifier to the left of the :: symbol identifies the namespace that the name to the right of the :: symbol is contained within. If no identifier to the left of the :: symbol is provided, the global namespace is assumed.

So when we say *std::cout*, we’re saying “the *cout* that lives in namespace *std*“.

This is the safest way to use *cout*, because there’s no ambiguity about which *cout* we’re referencing (the one in the *std* namespace).

### 2.9 Introduction to the preprocessor

When you compile your code, you might expect that the compiler compiles the code exactly as you’ve written it. This actually isn’t the case.

The *conditional compilation* preprocessor directives allow you to specify under what conditions something will or won’t compile. There are quite a few different conditional compilation directives, but we’ll only cover the three that are used by far the most here: *#ifdef*, *#ifndef*, and *#endif*.

The *#ifdef* preprocessor directive allows the preprocessor to check whether an identifier has been previously *#define*d. If so, the code between the *#ifdef* and matching *#endif* is compiled. If not, the code is ignored.

```C++
#include <iostream>
#define PRINT_JOE
int main()
{
#ifdef PRINT_JOE
    std::cout << "Joe\n"; // if PRINT_JOE is defined, compile this code
#endif
 
#ifdef PRINT_BOB
    std::cout << "Bob\n"; // if PRINT_BOB is defined, compile this code
#endif
    return 0;
}
```

*#ifndef* is the opposite of *#ifdef*, in that it allows you to check whether an identifier has *NOT* been *#define*d yet.

One more common use of conditional compilation involves using *#if 0* to exclude a block of code from being compiled (as if it were inside a comment block):

This provides a convenient way to “comment out” code that contains multi-line comments.

Once the preprocessor has finished, all defined identifiers from that file are discarded. This means that directives are only valid from the point of definition to the end of the file in which they are defined. Directives defined in one code file do not have impact on other code files in the same project.

function.app

```C++
#include <iostream>
void doSomething()
{
#ifdef PRINT
    std::cout << "Printing!";
#endif
#ifndef PRINT
    std::cout << "Not printing!";
#endif
}
```

main.app

```C++
void doSomething(); // forward declaration for function doSomething()
#define PRINT
int main()
{
    doSomething(); 
    return 0;
}
```

The above program will print

```
Not printing!
```

Even though PRINT was defined in *main.cpp*, that doesn’t have any impact on any of the code in *function.cpp* (PRINT is only #defined from the point of definition to the end of main.cpp). This will be of consequence when we discuss header guards in a future lesson.

### 2.10 Header files

As programs grow larger (and make use of more files), it becomes increasingly tedious to have to forward declare every function you want to use that is defined in a different file. Wouldn’t it be nice if you could put all your forward declarations in one place and then import them when you need them?

C++ code files (with a .cpp extension) are not the only files commonly seen in C++ programs. The other type of file is called a **header file**. Header files usually have a .h extension, but you will occasionally see them with a .hpp extension or no extension at all. The primary purpose of a header file is to propagate declarations to code files.

**Key insight**: Header files allow us to put declarations in one location and then import them wherever we need them. This can save a lot of typing in multi-file programs.

Using Standard Library Header files

Considering the following program

```C++
#include <iostream>
 
int main()
{
    std::cout << "Hello, world!";
    return 0;
}
```

This program prints “Hello, world!” to the console using *std::cout*. However, this program never provided a definition or declaration for *std::cout*, so how does the compiler know what *std::cout* is?

The answer is that *std::cout* has been forward declared in the “iostream” header file. When we `#include <iostream>`, we’re requesting that the preprocessor copy all of the content (including forward declarations for std::cout) from the file named “iostream” into the file doing the #include.

**Key Insight**: When you #include a file, the content of the included file is inserted at the point of inclusion. This provides a useful way to pull in declarations from another file.

When it comes to functions and variables, it’s worth keeping in mind that header files typically only contain function and variable declarations, not function and variable definitions (otherwise a violation of the *one definition rule* could result). *std::cout* is forward declared in the iostream header, but defined as part of the C++ standard library, which is automatically linked into your program during the linker phase.

**Best Practice**: If a header file is paired with a code file (e.g. add.h with add.cpp), they should both have the same base name (add).

![IncludeHeaders](D:\Study\学习笔记\Images\IncludeHeader.png)

Angled Brackets vs. double Quotes

When we use angled brackets, we’re telling the preprocessor that this is a header file we didn’t write ourselves. The compiler will search for the header only in the directories specified by the `include directories`. The `include directories` are configured as part of your project/IDE settings/compiler settings, and typically default to the directories containing the header files that come with your compiler and/or OS. The compiler will not search for the header file in your project’s source code directory.

When we use double-quotes, we’re telling the preprocessor that this is a header file that we wrote. The compiler will first search for the header file in the current directory. If it can’t find a matching header there, it will then search the `include directories`.

**Rule**: Use double quotes to include header files that you’ve written or are expected to be found in the current directory. Use angled brackets to include headers that come with your compiler, OS, or third-party libraries you’ve installed elsewhere on your system.

If your header files are written properly and #include everything they need, the order of inclusion shouldn’t matter. However, including your header files in a certain order can help surface mistakes where your header files may not include everything they need.

Using g++, you can use the -I option to specify an alternate include directory.

> g++ -o main -I/source/includes main.cpp

**Best Practice**: Order your #includes as follows: your own user-defined headers first, then 3rd party library headers, then standard library headers, with the headers in each section sorted alphabetically.

Thus, a program that defines a variable identifier more than once will cause a compile error. Similarly, programs that define a function more than once will also cause a compile error. Therefore, we noted that a variable or function identifier can only have **one definition (the one definition rule)**.

Header Guards

The good news is that we can avoid the above problem via a mechanism called a **header guard** (also called an **include guard**). Header guards are conditional compilation directives that take the following form:

```c++
#ifndef SOME_UNIQUE_NAME_HERE
#define SOME_UNIQUE_NAME_HERE
 
// your declarations (and certain types of definitions) here
 
#endif
```

Many compilers support a simpler, alternate form of header guards using the *#pragma* directive:

```c++
#pragma once
 
// your code here
```

`#pragma once` serves the same purpose as header guards, and has the added benefit of being shorter and less error-prone.

However, `#pragma once` is not an official part of the C++ language, and not all compilers support it (although most modern compilers do).

For compatibility purposes, we recommend sticking to traditional header guards. They aren’t much more work and they’re guaranteed to be supported on all compliant compilers.

How to design your programs?

When you sit down to write a program, generally you have some kind of idea, which you’d like to write a program for. New programmers often have trouble figuring out how to convert that idea into actual code. But it turns out, you have many of the problem solving skills you need already, acquired from everyday life.

A little up-front planning will save you both time and frustration in the long run.

**Design step 1: Define your goal**. In order to write a successful program, you first need to define what your goal is. Ideally, you should be able to state this in a sentence or two. It is often useful to express this as a user-facing outcome. The worst thing you can do is write a program that doesn’t actually do what you (or your boss) wanted!

**Design step 2: Define requirements**. 

While defining your problem helps you determine *what* outcome you want, it’s still vague. The next step is to think about requirements.

Requirements is a fancy word for both the constraints that your solution needs to abide by (e.g. budget, timeline, space, memory, etc…), as well as the capabilities that the program must exhibit in order to meet the users’ needs. Note that your requirements should similarly be focused on the “what”, not the “how”.

**Design step 3: Define your tools, targets, and backup plan**. 

When you are an experienced programmer, there are many other steps that typically would take place at this point, including:

- Defining what target architecture and/or OS your program will run on.
- Determining what set of tools you will be using.
- Determining whether you will write your program alone or as part of a team.
- Defining your testing/feedback/release strategy.
- Determining how you will back up your code.

However, as a new programmer, the answers to these questions are typically simple: You are writing a program for your own use, alone, on your own system, using an IDE you purchased or downloaded, and your code is probably not used by anybody but you. This makes things easy.

**Design step 4: Break hard problems down into easy problems**. In real life, we often need to perform tasks that are very complex. Trying to figure out how to do these tasks can be very challenging. In such cases, we often make use of the **top down** method of problem solving. That is, instead of solving a single complex task, we break that task into multiple subtasks, each of which is individually easier to solve. If those subtasks are still too difficult to solve, they can be broken down further. By continuously splitting complex tasks into simpler ones, you can eventually get to a point where each individual task is manageable, if not trivial.

The other way to create a hierarchy of tasks is to do so from the **bottom up**. In this method, we’ll start from a list of easy tasks, and construct the hierarchy by grouping them.

**Design step 5: Figure out the sequence of events**. Now that your program has a structure, it’s time to determine how to link all the tasks together. The first step is to determine the sequence of events that will be performed. 

**Implementation step 1: Outlining your main function**. Now we’re ready to start implementation. The above sequences can be used to outline your main program. 

**Implementation step 2: Implement each function**. In this step, for each function, you’ll do three things:

1. Define the function prototype (inputs and outputs)
2. Write the function
3. Test the function

**Implementation step 3: Final testing**. Once your program is “finished”, the last step is to test the whole program and ensure it works as intended. If it doesn’t work, fix it.

**Words of advice when writing programs**. 

**Keep your programs simple to start**. Often new programmers have a grand vision for all the things they want their program to do. “I want to write a role-playing game with graphics and sound and random monsters and dungeons, with a town you can visit to sell the items that you find in the dungeon” If you try to write something too complex to start, you will become overwhelmed and discouraged at your lack of progress. Instead, make your first goal as simple as possible, something that is definitely within your reach. For example, “I want to be able to display a 2-dimensional field on the screen”.

**Add features over time**. Once you have your simple program working and working well, then you can add features to it. For example, once you can display your field, add a character who can walk around. Once you can walk around, add walls that can impede your progress. Once you have walls, build a simple town out of them. Once you have a town, add merchants. By adding each feature incrementally your program will get progressively more complex without overwhelming you in the process.

**Focus on one area at a time**. Don’t try to code everything at once, and don’t divide your attention across multiple tasks. Focus on one task at a time. It is much better to have one working task and five that haven’t been started yet than six partially-working tasks. If you split your attention, you are more likely to make mistakes and forget important details.

**Test each piece of code as you go**. New programmers will often write the entire program in one pass. Then when they compile it for the first time, the compiler reports hundreds of errors. This can not only be intimidating, if your code doesn’t work, it may be hard to figure out why. Instead, write a piece of code, and then compile and test it immediately. If it doesn’t work, you’ll know exactly where the problem is, and it will be easy to fix. Once you are sure that the code works, move to the next piece and repeat. It may take longer to finish writing your code, but when you are done the whole thing should work, and you won’t have to spend twice as long trying to figure out why it doesn’t.

**Don’t invest in perfecting early code**. The first draft of a feature (or program) is rarely good. Furthermore, programs tend to evolve over time, as you add capabilities and find better ways to structure things. If you invest too early in polishing your code (adding lots of documentation, full compliance with best practices, making optimizations), you risk losing all of that investment when a code change is necessary. Instead, get your features minimally working and then move on. As you gain confidence in your solutions, apply successive layers of polish. Don’t aim for perfect -- non-trivial programs are never perfect, and there’s always something more that could be done to improve them. Get to good enough and move on.

Most new programmers will shortcut many of these steps and suggestions (because it seems like a lot of work and/or it’s not as much fun as writing the code). However, for any non-trivial project, following these steps will definitely save you a lot of time in the long run. A little planning up front saves a lot of debugging at the end.

The good news is that once you become comfortable with all of these concepts, they will start coming more naturally to you. Eventually you will get to the point where you can write entire functions without any pre-planning at all.