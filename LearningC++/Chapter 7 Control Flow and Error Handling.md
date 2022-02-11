## Chapter 7 Control Flow and Error Handling

When a program is run, the CPU begins execution at the top of `main()`, executes some number of statements (in sequential order by default), and then the program terminates at the end of `main()`. The specific sequence of statements that the CPU executes is called the program’s **execution path** (or **path**, for short).

Straight-line programs take the same path (execute the same statements in the same order) every time they are run.

C++ provides a number of different **control flow statements** (also called **flow control statements**), which are statements that allow the programmer to change the normal path of execution through the program.

When a `control flow statement` causes point of execution to change to a non-sequential statement, this is called **branching**.

**Categories of Control Flow Statements**

| Category               | Meaning                                                      | Implementing in C++ by           |
| ---------------------- | ------------------------------------------------------------ | -------------------------------- |
| Conditional Statements | Conditional statements cause a sequence of code to execute only if some condition is met. | If, switch                       |
| Jumps                  | Jumps tell the CPU to start executing the statements at some other location. | Goto, break, continue            |
| Loops                  | Loops tell the program to repeatedly execute some sequence of code zero or more times, until some condition is met. | While, do-while, for, ranged-for |
| Function Calls         | Function calls are jumps to some other location and back.    | Function calls, return           |
| Halts                  | Halts tell the program to quit running.                      | std::exit(), std::abort()        |
| Exceptions             | Exceptions are a special kind of flow control structure designed for error handling. | Try, throw, catch                |

The idea behind a **switch statement** is simple: an expression (sometimes called the `condition`) is evaluated to produce a value. If the expression’s value is equal to the value after any of the `case labels`, the statements after the matching `case label` are executed. If no matching value can be found and a `default label` exists, the statements after the `default label` are executed instead.

Compared to the original `if statement`, the `switch statement` has the advantage of only evaluating the expression once (making it more efficient), and the `switch statement` also makes it clearer to the reader that it is the same expression being tested for equality in each case.

**Best Practice**: Prefer `switch statements` over if-else chains when there is a choice.

The one restriction is that the condition must evaluate to an integral type or an enumerated type, or be convertable to one. Expressions that evaluate to floating point types, strings, and most other non-integral types may not be used here.

**For Advanced Readers**: Why does the switch type only allow for integral (or enumerated) types? The answer is because switch statements are designed to be highly optimized. Historically, the most common way for compilers to implement switch statements is via a [Jump tables](https://en.wikipedia.org/wiki/Branch_table) -- and jump tables only work with integral values.

For those of you already familiar with arrays, a jump table works much like an array, an integral value is used as the array index to “jump” directly to a result. This can be much more efficient than doing a bunch of sequential comparisons.

Of course, compilers don’t have to implement switches using jump tables, and sometimes they don’t. There is technically no reason that C++ couldn’t relax the restriction so that other types could be used as well, they just haven’t done so yet (as of C++20).

A **break statement** (declared using the `break` keyword) tells the compiler that we are done executing statements within the switch, and that execution should continue with the statement after the end of the switch block. This allows us to exit a `switch statement` without exiting the entire function.

C++17 adds a new attribute called `[[fallthrough]]`.

**Attributes** are a modern C++ feature that allows the programmer to provide the compiler with some additional data about the code. To specify an attribute, the attribute name is placed between double hard braces. Attributes are not statements -- rather, they can be used almost anywhere where they are contextually relevant.

```C++
#include <iostream>
 
int main()
{
    switch (2)
    {
    case 1:
        std::cout << 1 << '\n';
        break;
    case 2:
        std::cout << 2 << '\n'; // Execution begins here
        [[fallthrough]]; // intentional fallthrough -- note the semicolon to indicate the null statement
    case 3:
        std::cout << 3 << '\n'; // This is also executed
        break;
    }
 
    return 0;
}
```

**Best Practice**: Use the `[[fallthrough]]` attribute (along with a null statement) to indicate intentional fallthrough.

With `if statements`, you can only have a single statement after the if-condition, and that statement is considered to be implicitly inside a block:

```C++
if (x > 10)
    std::cout << x << " is greater than 10\n"; // this line implicitly considered to be inside a block
```

However, with switch statements, the statements after labels are all scoped to the the switch block. No implicit blocks are created.

```C++
switch (1)
{
    case 1:
        foo();
        break;
    default:
        std::cout << "default case\n";
        break;
}
```

In the above example, the 2 statements between the `case 1` and the default label are scoped as part of the switch block, not a block implicit to `case 1`.

You can declare (but not initialize) variables inside the switch, both before and after the case labels:

You can declare (but not initialize) variables inside the switch, both before and after the case labels:

```C++
switch (1)
{
    int a; // okay: declaration is allowed before the case labels
    int b{ 5 }; // illegal: initialization is not allowed before the case labels
 
    case 1:
        int y; // okay but bad practice: declaration is allowed within a case
        y = 4; // okay: assignment is allowed
        break;
 
    case 2:
        y = 5; // okay: y was declared above, so we can use it here too
        break;
 
    case 3:
        int z{ 4 }; // illegal: initialization is not allowed within a case
        break;
}
```

Note that although variable `y` was defined in `case 1`, it was used in `case 2` as well. Because the statements under each case are not inside an implicit block, that means all statements inside the switch are part of the same scope. Thus, a variable defined in one case can be used in a later case, even if the case in which the variable is defined is never executed! Put another way, defining a variable without an initializer is just telling the compiler that the variable is now in scope from that point on. This doesn’t require the definition to actually be executed.

However, initialization of variables is disallowed and will cause a compile error. This is because initializing a variable *does* require execution, and initialization could be skipped over depending on which cases are executed.

If a case needs to define and/or initialize a new variable, best practice is to do so inside a block underneath the case statement:

```C++
switch (1)
{
    case 1:
    { // note addition of block here
        int x{ 4 }; // okay, variables can be initialized inside a block inside a case
        std::cout << x;
        break;
    }
    default:
        std::cout << "default case\n";
        break;
}
```

The next kind of control flow statement we’ll cover is the unconditional jump. An unconditional jump causes execution to jump to another spot in the code. The term “unconditional” means the jump always happens (unlike an `if statement` or `switch statement`, where the jump only happens conditionally based on the result of an expression).

In C++, unconditional jumps are implemented via a **goto statement**, and the spot to jump to is identified through use of a **statement label**. The following is an example of a `goto statement` and `statement label`:

```C++
#include <iostream>
#include <cmath> // for sqrt() function
 
int main()
{
    double x{};
tryAgain: // this is a statement label
    std::cout << "Enter a non-negative number: "; 
    std::cin >> x;
 
    if (x < 0.0)
        goto tryAgain; // this is the goto statement
 
    std::cout << "The square root of " << x << " is " << std::sqrt(x) << '\n';
    return 0;
}
```

In the chapter on object scope, we covered two kinds of scope: local (block) scope, and file (global) scope. Statement labels utilize a third kind of scope: **function scope**, which means the label is visible throughout the function even before its point of declaration. The `goto statement` and its corresponding `statement label` must appear in the same function.

**Avoid using goto**: Almost any code written using a `goto statement` can be more clearly written using other constructs in C++, such as `if statements` and loops. One notable exception is when you need to exit a nested loop but not the entire function -- in such a case, a `goto` to just beyond the loops is probably the cleanest solution.

In practice, do-while loops aren’t commonly used. Having the condition at the bottom of the loop obscures the loop condition, which can lead to errors. Many developers recommend avoiding do-while loops altogether as a result. We’ll take a softer stance and advocate for preferring while loops over do-while when given an equal choice.

One of the biggest problems that new programmers have with `for loops` (and other loops that utilize counters) are `off-by-one errors`. **Off-by-one errors** occur when the loop iterates one too many or one too few times to produce the desired result.

**Best Practice**: Prefer `for loops` over `while loops` when there is an obvious loop variable. Prefer `while loops` over `for loops` when there is no obvious loop variable.

In the context of a loop, a break statement can be used to end the loop early. Execution continues with the next statement after the end of the loop. Break is also a common way to get out of an intentional infinite loop. 

New programmers sometimes have trouble understanding the difference between `break` and `return`. A `break statement` terminates the switch or loop, and execution continues at the first statement beyond the switch or loop. A `return statement` terminates the entire function that the loop is within, and execution continues at point where the function was called.

The **continue statement** provides a convenient way to end the current iteration of a loop without terminating the entire loop. `Continue statements` work by causing the current point of execution to jump to the bottom of the current loop.

Minimizing the number of variables used and keeping the number of nested blocks down both improve code comprehensibility more than a `break` or `continue` harms it. For that reason, we believe judicious use of `break` or `continue` is acceptable.

**Best Practice**: Use break and continue when they simplify your loop logic. Use early returns when they simplify your function’s logic.

There’s a similar argument to be made for return statements. A return statement that is not the last statement in a function is called an **early return**. Many programmers believe early returns should be avoided. A function that only has one return statement at the bottom of the function has a simplicity to it -- you can assume the function will take its arguments, do whatever logic it has implemented, and return a result without deviation. Having extra returns complicates the logic.

The counter-argument is that using early returns allows your function to exit as soon as it is done, which reduces having to read through unnecessary logic and minimizes the need for conditional nested blocks, which makes your code more readable.

Some developers take a middle ground, and only use early returns at the top of a function to do parameter validation (catch bad arguments passed in), and then a single return thereafter.

Our stance is that early returns are more helpful than harmful, but we recognize that there is a bit of art to the practice.

A **halt** is a flow control statement that terminates the program. In C++, halts are implemented as functions (rather than keywords), so our halt statements will be function calls.

Let’s take a brief detour, and recap what happens when a program exits normally. When the `main()` function returns (either by reaching the end of the function, or via a `return statement`), a number of different things happen.

First, because we’re leaving the function, all local variables and function parameters are destroyed (as per usual).

Next, a special function called `std::exit()` is called, with the return value from `main()` (the `status code`) passed in as an argument. So what is `std::exit()`?

`std::exit()` is a function that causes the program to terminate normally. **Normal termination** means the program has exited in an expected way. Note that the term `normal termination` does not imply anything about whether the program was successful (that’s what the `status code` is for). For example, let’s say you were writing a program where you expected the user to type in a filename to process. If the user typed in an invalid filename, your program would probably return a non-zero `status code` to indicate the failure state, but it would still have a `normal termination`.

`std::exit()` performs a number of cleanup function. First, objects with static storage duration are destroyed. Then some other miscellaneous file cleanup is done if any files were used. Finally, control is returned back to the OS, with the argument passed to `std::exit()` used as the `status code`.

One important note about calling `std::exit()` explicitly: `std::exit()` does not clean up any local variables (either in the current function, or in functions up the `call stack`). Because of this, it’s generally better to avoid calling `std::exit()`.

Because `std::exit()` terminates the program immediately, you may want to manually do some cleanup (in the above example, we manually called function `cleanup()`) before terminating. Remembering to manually call a cleanup function before calling every call to exit() adds burden to the programmer.

To assist with this, C++ offers the `std::atexit()` function, which allows you to specify a function that will automatically be called on program termination via `std:exit()`.

**For advanced readers**: In multi-threaded programs, calling `std::exit()` can cause your program to crash (because the thread calling `std::exit()` will cleanup static objects that may still be accessed by other threads). For this reason, C++ has introduced another pair of functions that work similarly to `std::exit()` and `std::atexit()` called `std::quick_exit()` and `std::at_quick_exit()`. `std::quick_exit()` terminates the program normally, but does not clean up static objects, and may or may not do other types of cleanup. `std::at_quick_exit()` performs the same role as `std::atexit()` for programs terminated with `std::quick_exit()`.

The `std::abort()` function causes your program to terminate abnormally. **Abnormal termination** means the program had some kind of unusual runtime error and the program couldn’t continue to run. For example, trying to divide by 0 will result in an abnormal termination. `std::abort()` does not do any cleanup.

The `std::terminate()` function is typically used in conjunction with `exceptions` (we’ll cover exceptions in a later chapter). Although `std::terminate` can be called explicitly, it is more often called implicitly when an exception isn’t handled (and in a few other exception-related cases). By default, `std::terminate()` calls `std::abort()`.

**When should you use a halt?**: The short answer is “almost never”. Destroying local objects is an important part of C++ (particularly when we get into classes), and none of the above-mentioned functions clean up local variables. Exceptions are a better and safer mechanism for handling error cases. 

**Best Practice**: Only use a halt if there is no safe way to return normally from the main function. If you haven’t disabled exceptions, prefer using exceptions for handling errors safely.

Just because your program worked for one set of inputs doesn’t mean it’s going to work correctly in all cases. **Software verification** (a.k.a. **software testing**) is the process of determining whether or not the software works as expected in all cases.

**Best practice**: Write your program in small, well defined units (functions or classes), compile often, and test your code as your go.

If the program is short and accepts user input, trying a variety of user inputs might be sufficient. But as programs get longer and longer, this becomes less sufficient, and there is more value in testing individual functions or classes before integrating them into the rest of the program.

So how can we test our code in units? **Unit testing frameworks**: Because writing functions to exercise other functions is so common and useful, there are entire frameworks (called **unit testing frameworks**) that are designed to help simplify the process of writing, maintaining, and executing unit tests. Since these involve third party software, we won’t cover them here, but you should be aware they exist.

Once each of your units has been tested in isolation, they can be integrated into your program and retested to make sure they were integrated properly. This is called an **integration test**. Integration testing tends to be more complicated.

**Code Coverage**: The term **code coverage** is used to describe how much of the source code of a program is executed while testing. There are many different metrics used for code coverage. We’ll cover a few of the more useful and popular ones in the following sections.

**Branch coverage** refers to the percentage of branches that have been executed, each possible branch counted separately. An `if statement` has two branches -- a branch that executes when the condition is `true`, and a branch that executes when the condition is `false` (even if there is no corresponding `else statement` to execute). A switch statement can have many branches.

**Best Practice**: Aim for 100% branch coverage of your code.

**Loop coverage** (informally called **the 0, 1, 2 test**) says that if you have a loop in your code, you should ensure it works properly when it iterates 0 times, 1 time, and 2 times. If it works correctly for the 2-iteration case, it should work correctly for all iterations greater than 2. These three tests therefore cover all possibilities (since a loop can’t execute a negative number of times).

**Best Practice**: Use the `0, 1, 2 test` to ensure your loops work correctly with different number of iterations.

**Test different categories of input**

When writing functions that accept parameters, or when accepting user input, consider what happens with different categories of input. In this context, we’re using the term “category” to mean a set of inputs that have similar characteristics.

For example, if I wrote a function to produce the square root of an integer, what values would it make sense to test it with? You’d probably start with some normal value, like `4`. But it would also be a good idea to test with `0`, and a negative number.

Here are some basic guidelines for category testing:

For integers, make sure you’ve considered how your function handles negative values, zero, and positive values. You should also check for overflow if that’s relevant.

For floating point numbers, make sure you’ve considered how your function handles values that have precision issues (values that are slightly larger or smaller than expected). Good test values are `0.1` and `-0.1` (to test numbers that are slightly larger than expected) and `0.6` and `-0.6` (to test numbers that are slightly smaller than expected).

For strings, make sure you’ve considered how your function handles an empty string (just a null terminator), normal valid strings, strings that have whitespace, and strings that are all whitespace.

If your function takes a pointer, don’t forget to test `nullptr` as well (don’t worry if this doesn’t make sense, we haven’t covered it yet).

**Best practice**: Test different categories of input values to make sure your unit handles them properly.

**Two common Code Errors**: In previous lesson, we covered `syntax errors`, which occur when you write code that is not valid according to the grammar of the C++ language. The compiler will notify of you these types of errors, so they are trivial to catch, and usually straightforward to fix.

We also covered `semantic errors`, which occur when you write code that does not do what you intended. The compiler generally will not catch semantic errors (though in some cases, smart compilers may be able to generate a warning).

Semantic errors can cause most of the same symptoms of `undefined behavior`, such as causing the program to produce the wrong results, causing erratic behavior, corrupting program data, causing the program to crash -- or they may not have any impact at all.

One of the most common types of semantic errors is a conditional logic error. A **conditional logic error** occurs when the programmer incorrectly codes the logic of a conditional statement or loop condition. 

An **off-by-one** error is an error that occurs when a loop executes one too many or one too few times. 

Common Semantic Errors:

- conditional logic error
- off-by-one

- Infinite loops
- Incorrect operator precedence
- Precision issues with floating point types
- Integer division
- Accidental null statements
- dangling if statements
- ...

**Handling errors in functions**:

There are 4 general strategies that can be used:

- Handle the error within the function

    If possible, the best strategy is to recover from the error in the same function in which the error occurred, so that the error can be contained and corrected without impacting any code outside the function. There are two options here: retry until successful, or cancel the operation being executed.

    An alternate strategy is just to ignore the error and/or cancel the operation. The primary challenge with doing this is that the caller or user have no way to identify that something went wrong. In such case, printing an error message can be helpful. However, if the calling function is expecting the called function to produce a return value or some useful side-effect, then just ignoring the error may not be an option.

- Pass the error back to the caller to deal with

    The best option can be to pass the error back to the caller in hopes that the caller will be able to deal with it.

- Halt the program

    If the error is so bad that the program can not continue to operate properly, this is called a **non-recoverable** error (also called a **fatal error**). In such cases, the best thing to do is terminate the program. If your code is in `main()` or a function called directly from `main()`, the best thing to do is let `main()` return a non-zero status code. However, if you’re deep in some nested subfunction, it may not be convenient or possible to propagate the error all the way back to `main()`. In such a case, a `halt statement` (such as `std::exit()`) can be used.

- Throw an exception

    Because returning an error from a function back to the caller is complicated (and the many different ways to do so leads to inconsistency, and inconsistency leads to mistakes), C++ offers an entirely separate way to pass errors back to the caller: `exceptions`.

    The basic idea is that when an error occurs, an exception is “thrown”. If the current function does not “catch” the error, the caller of the function has a chance to catch the error. If the caller does not catch the error, the caller’s caller has a chance to catch the error. The error progressively moves up the call stack until it is either caught and handled (at which point execution continues normally), or until main() fails to handle the error (at which point the program is terminated with an exception error).

As you write programs, you should always consider how users will (unintentionally or otherwise) misuse your programs. A well-written program will anticipate how users will misuse it, and either handle those cases gracefully or prevent them from happening in the first place (if possible). A program that handles error cases well is said to be **robust**.

When the user enters input in response to an extraction operation, that data is placed in a buffer inside of std::cin. A **buffer** (also called a data buffer) is simply a piece of memory set aside for storing data temporarily while it’s moved from one place to another. In this case, the buffer is used to hold user input while it’s waiting to be extracted to variables.

When the extraction operator is used, the following procedure happens:

- If there is data already in the input buffer, that data is used for extraction.
- If the input buffer contains no data, the user is asked to input data for extraction (this is the case most of the time). When the user hits enter, a `‘\n’ `character will be placed in the input buffer.
- `operator>> `extracts as much data from the input buffer as it can into the variable (ignoring any leading whitespace characters, such as spaces, tabs, or `‘\n’`).
- Any data that can not be extracted is left in the input buffer for the next extraction.

Extraction succeeds if at least one character is extracted from the input buffer. Any unextracted input is left in the input buffer for future extractions. 

Extraction fails if the input data does not match the type of the variable being extracted to. 

The process of checking whether user input conforms to what the program is expecting is called **input validation**.

There are three basic ways to do input validation:

- Inline (as the user types)

- - Prevent the user from typing invalid input in the first place.

- Post-entry (after the user types)

- - Let the user enter whatever they want into a string, then validate whether the string is correct, and if so, convert the string to the final variable format.
        - Let the user enter whatever they want, let std::cin and operator>> try to extract it, and handle the error cases.

**Types of invalid text input**

We can generally separate input text errors into four types:

- Input extraction succeeds but the input is meaningless to the program (e.g. entering ‘k’ as your mathematical operator).
- Input extraction succeeds but the user enters additional input (e.g. entering ‘*q hello’ as your mathematical operator).
- Input extraction fails (e.g. trying to enter ‘q’ into a numeric input).
- Input extraction succeeds but the user overflows a numeric value.



An **assertion** is an expression that will be true unless there is a bug in the program. If the expression evaluates to `true`, the assertion statement does nothing. If the conditional expression evaluates to `false`, an error message is displayed and the program is terminated (via `std::abort`). This error message typically contains the expression that failed as text, along with the name of the code file and the line number of the assertion. This makes it very easy to tell not only what the problem was, but where in the code the problem occurred. This can help with debugging efforts immensely.

Although asserts are most often used to validate function parameters, they can be used anywhere you would like to validate that something is true.

Although we told you previously to avoid preprocessor macros, asserts are one of the few preprocessor macros that are considered acceptable to use. We encourage you to use assert statements liberally throughout your code.

Assertions and error handling are similar enough that their purposes can be confused, so let’s clarify:

The goal of an assertion is to catch programming errors by documenting something that should never happen. If that thing does happen, then the programmer made an error somewhere, and that error can be identified and fixed. Assertions do not allow recovery from errors (after all, if something should never happen, there’s no need to recover from it), and the program will not produce a friendly error message.

On the other hand, error handling is designed to gracefully handle cases that could happen (however rarely) in release configurations. These may or may not be recoverable, but one should always assume a user of the program may encounter them.

**Best practice**: Use assertions to document cases that should be logically impossible.

