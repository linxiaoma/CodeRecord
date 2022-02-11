## Chapter 1 Introduction/Getting started

Good code comments style:

Typically, comments should be used for **three things**, i.e., what, how, and why:

1. First, for a given library, program, or function, comments are best used to describe *what* the library, program, or function, does. These are typically placed at the top of the file or library, or immediately preceding the function. 
2. Second, *within* a library, program, or function described above, comments can be used to describe *how* the code is going to accomplish its goal.
3. Third, at the statement level, comments should be used to describe *why* the code is doing something. A bad statement comment explains *what* the code is doing. If you ever write code that is so complex that needs a comment to explain *what* a statement is doing, you probably need to rewrite your statement, not comment it.

Converting one or more lines of code into a comment is called **commenting out** your code. This provides a convenient way to (temporarily) exclude parts of your code from being included in your compiled program.

Programs are collections of instructions that manipulate data to produce a desired result.

In C++, direct memory access is not allowed. Instead, we access memory indirectly through an object. An **object** is a region of storage (usually memory) that has a value and other associated properties (that we’ll cover in future lessons). How the compiler and operating system work to assign memory to objects is beyond the scope of this lesson. But the key point here is that rather than say *go get the value stored in mailbox number 7532*, we can say, *go get the value stored by this object*. This means we can focus on using objects to store and retrieve values, and not have to worry about where in memory they’re actually being placed.

Use an explicit initialization value if you’re actually using that value.

```c++
int x { 0 }; // explicit initialization to value 0
std::cout << x; // we're using that zero value
```

Use value initialization if the value is temporary and will be replaced.

```c++
int x {}; // value initialization
std::cin >> x; // we're immediately replacing that value
```

Using std::endl can be a bit inefficient, as it actually does two jobs: it moves the cursor to the next line, and it “flushes” the output (makes sure that it shows up on the screen immediately). When writing text to the console using std::cout, std::cout usually flushes output anyway (and if it doesn’t, it usually doesn’t matter), so having std::endl flush is rarely important.

Because of this, use of the ‘\n’ character is typically preferred instead. The ‘\n’ character moves the cursor to the next line, but doesn’t do the redundant flush, so it performs better. The ‘\n’ character also tends to be easier to read since it’s both shorter and can be embedded into existing text.

Best Practice: Prefer ‘\n’ over std::endl when outputting text to the console.

For Advanced Readers: The C++ io library does not provide a way to accept keyboard input without the user having to press *enter*. If this is something you desire, you’ll have to use a third party library. For console applications, we’d recommend the [pdcurses](https://pdcurses.org/) library. Many graphical user libraries have their own functions to do this kind of thing.

New programmers often mix up std::cin, std::cout, the insertion operator (`<<`) and the extraction operator (`>>`). Here’s an easy way to remember:

- `std::cin` and `std::cout` always go on the left-hand side of the statement.
- `std::cout` is used to output a value (cout = character output)
- `std::cin` is used to get an input value (cin = character input)
- `<<` is used with std::cout, and shows the direction that data is moving (if `std::cout` represents the console, the output data is moving from the variable to the console). `std::cout << 4` moves the value of 4 to the console
- `>>` is used with `std::cin`, and shows the direction that data is moving (if std::cin represents the keyboard, the input data is moving from the keyboard to the variable). `std::cin >> x` moves the value the user entered from the keyboard into x

Undefined Behaviors

Code implementing undefined behavior may exhibit *any* of the following symptoms:

- Your program produces different results every time it is run.
- Your program consistently produces the same incorrect result.
- Your program behaves inconsistently (sometimes produces the correct result, sometimes not).
- Your program seems like its working but produces incorrect results later in the program.
- Your program crashes, either immediately or later.
- Your program works on some compilers but not others.
- Your program works until you change some other seemingly unrelated code.

Keywords

C++ reserves a set of 92 words (as of C++20) for its own use. These words are called **keywords** (or reserved words), and each of these keywords has a special meaning within the C++ language.

Here is a list of all the C++ keywords (through C++20):

| alignas                 | alignof                 | and                     | and_eq                 | asm           |
| ----------------------- | ----------------------- | ----------------------- | ---------------------- | ------------- |
| auto                    | bitand                  | bitor                   | bool                   | break         |
| case                    | catch                   | char                    | char8_t (since C++20)  | char16_t      |
| char32_t                | class                   | compl                   | concept (since C++20)  | const         |
| consteval (since C++20) | constexpr               | constinit (since C++20) | const_cast             | continue      |
| co_await (since C++20)  | co_return (since C++20) | co_yield (since C++20)  | decltype               | default       |
| delete                  | do                      | double                  | dynamic_cast           | else          |
| enum                    | explicit                | export                  | extern                 | false         |
| float                   | for                     | friend                  | goto                   | if            |
| inline                  | int                     | long                    | mutable                | namespace     |
| new                     | noexcept                | not                     | not_eq                 | nullptr       |
| operator                | or                      | or_eq                   | private                | protected     |
| public                  | register                | reinterpret_cast        | requires (since C++20) | return        |
| short                   | signed                  | sizeof                  | static                 | static_assert |
| static_cast             | struct                  | switch                  | template               | this          |
| thread_local            | throw                   | true                    | try                    | typedef       |
| typeid                  | typename                | union                   | unsigned               | using         |
| virtual                 | void                    | volatile                | wchar_t                | while         |
| xor                     | xor_eq                  |                         |                        |               |

The keywords marked (C++20) were added in C++20. If your compiler is not C++20 compliant (or does have C++20 functionality, but it’s turned off by default), these keywords may not be functional.

Identifier Naming Rules

As a reminder, the name of a variable (or function, type, or other kind of item) is called an identifier. C++ gives you a lot of flexibility to name identifiers as you wish. However, there are a few rules that must be followed when naming identifiers:

- The identifier can not be a keyword. Keywords are reserved.
- The identifier can only be composed of letters (lower or upper case), numbers, and the underscore character. That means the name can not contain symbols (except the underscore) nor whitespace (spaces or tabs).
- The identifier must begin with a letter (lower or upper case) or an underscore. It can not start with a number.
- C++ is case sensitive, and thus distinguishes between lower and upper case letters. `nvalue` is different than `nValue` is different than `NVALUE`.

Identifier Naming Best Practice

Now that you know how you *can* name a variable, let’s talk about how you *should* name a variable (or function).

First, it is a convention in C++ that variable names should begin with a lowercase letter. If the variable name is one word, the whole thing should be written in lowercase letters.

Most often, function names are also started with a lowercase letter (though there’s some disagreement on this point). We’ll follow this convention, since function *main* (which all programs must have) starts with a lowercase letter, as do all of the functions in the C++ standard library.

Identifier names that start with a capital letter are typically used for user-defined types (such as structs, classes, and enumerations, all of which we will cover later).

If the variable or function name is multi-word, there are two common conventions: words separated by underscores, called snake_case, or intercapped (sometimes called camelCase, since the capital letters stick up like the humps on a camel).

It’s worth noting that if you’re working in someone else’s code, it’s generally considered better to match the style of the code you are working in than to rigidly follow the naming conventions laid out above.

Second, you should avoid naming your identifiers starting with an underscore, as these names are typically reserved for OS, library, and/or compiler use.

Third, your identifiers should make clear what the value they are holding means (particularly if the units aren’t obvious). Identifiers should be named in a way that would help someone who has no idea what your code does be able to figure it out as quickly as possible. In 3 months, when you look at your program again, you’ll have forgotten how it works, and you’ll thank yourself for picking variable names that make sense.

Finally, a clarifying comment can go a long way.

A **literal** (also known as a **literal constant**) is a fixed value that has been inserted directly into the source code.

Literals and variables both have a value (and a type). However, the value of a literal is fixed and can’t be changed (hence it being called a constant), whereas the value of a variable can be changed through initialization and assignment.

Operators in C++ come in three different *arities*:

- **Unary** operators act on one operand. An example of a unary operator is the *- operator*. For example, given `-5`, *operator-* takes literal operand *5* and flips its sign to produce new output value *-5*.
- **Binary** operators act on two operands (known as *left* and *right*). An example of a binary operator is the *+ operator*. For example, given `3 + 4`, *operator+* takes the left operand (3) and the right operand (4) and applies mathematical addition to produce new output value *7*. The insertion (<<) and extraction (>>) operators are binary operators, taking std::cout or std::cin on the left side, and the item to output or variable to input to on the right side.
- **Ternary** operators act on three operands. There is only one of these in C++, which we’ll cover later.

An **expression** is a combination of literals, variables, operators, and explicit function calls (not shown above) that produce a single output value. When an expression is executed, each of the terms in the expression is evaluated until a single value remains (this process is called **evaluation**). That single value is the **result** of the expression.

Best Practice

New programmers often try to write an entire program all at once, and then get overwhelmed when it produces a lot of errors. A better strategy is to add one piece at a time, make sure it compiles, and test it. Then when you’re sure it’s working, move on to the next piece.

