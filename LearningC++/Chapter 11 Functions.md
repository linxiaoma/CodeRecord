# Chapter 11 Functions

### 11.1 Function Parameters and Arguments

A **function parameter** (sometimes called a **formal parameter**) is a variable declared in the function declaration:

```C++
void foo(int x); // declaration (function prototype) -- x is a parameter
 
void foo(int x) // definition (also a declaration) -- x is a parameter
{
}
```

An **argument** (sometimes called an **actual parameter**) is the value that is passed to the function by the caller:

```C++
foo(6); // 6 is the argument passed to parameter x
foo(y+1); // the value of y+1 is the argument passed to parameter x
```

When a function is called, all of the parameters of the function are created as variables, and the value of the arguments are copied into the parameters. For example:

```C++
void foo(int x, int y)
{
}
 
foo(6, 7);
```

When foo() is called with arguments 6 and 7, foo’s parameter x is created and assigned the value of 6, and foo’s parameter y is created and assigned the value of 7.

Even though parameters are not declared inside the function block, function parameters have local scope. This means that they are created when the function is invoked, and are destroyed when the function block terminates:

```C++
void foo(int x, int y) // x and y are created here
{
} // x and y are destroyed here
```

There are 3 primary methods of passing arguments to functions: pass by value, pass by reference, and pass by address. We’ll look at each of those in the next set of lessons.

### 11.2 Passing Arguments by Value

By default, non-pointer arguments in C++ are passed by value. When an argument is **passed by value**, the argument’s value is copied into the value of the corresponding function parameter.

Because a copy of the argument is passed to the function, the original argument can not be modified by the function. 

**Pros and cons of pass by value**

Advantages of passing by value:

- Arguments passed by value can be variables (e.g. x), literals (e.g. 6), expressions (e.g. x+1), structs & classes, and enumerators. In other words, just about anything.
- Arguments are never changed by the function being called, which prevents side effects.

Disadvantages of passing by value:

- Copying structs and classes can incur a significant performance penalty, especially if the function is called many times.

When to use pass by value:

- When passing fundamental data types and enumerators, and the function does not need to change the argument.

When not to use pass by value:

- When passing structs or classes (including std::array, std::vector, and std::string).

In most cases, pass by value is the best way to accept parameters of fundamental types when the function does not need to change the argument. Pass by value is flexible and safe, and in the case of fundamental types, efficient.

### 11.3 Passing Arguments by Reference

While pass by value is suitable in many cases, it has a couple of limitations. First, when passing a large struct or class to a function, pass by value will make a copy of the argument into the function parameter. In many cases, this is a needless performance hit, as the original argument would have sufficed. Second, when passing arguments by value, the only way to return a value back to the caller is via the function’s return value. While this is often suitable, there are cases where it would be more clear and efficient to have the function modify the argument passed in. Pass by reference solves both of these issues.

**Pass by reference**

To pass a variable by reference, we simply declare the function parameters as references rather than as normal variables:

```C++
void addOne(int& ref) // ref is a reference variable
{
    ref = ref + 1;
}
```

When the function is called, ref will become a reference to the argument. Since a reference to a variable is treated exactly the same as the variable itself, any changes made to the reference are passed through to the argument!

**Returning multiple values via out parameters**

Sometimes we need a function to return multiple values. However, functions can only have one return value. One way to return multiple values is using reference parameters:

```C++
#include <iostream>
#include <cmath>    // for std::sin() and std::cos()
 
void getSinCos(double degrees, double& sinOut, double& cosOut)
{
    // sin() and cos() take radians, not degrees, so we need to convert
    constexpr double pi { 3.14159265358979323846 }; // the value of pi
    double radians{ degrees * pi / 180.0 };
    sinOut = std::sin(radians);
    cosOut = std::cos(radians);
}
 
int main()
{
    double sin{ 0.0 };
    double cos{ 0.0 };
 
    // getSinCos will return the sin and cos in variables sin and cos
    getSinCos(30.0, sin, cos);
 
    std::cout << "The sin is " << sin << '\n';
    std::cout << "The cos is " << cos << '\n';
    return 0;
}
```

This function takes one parameter (by value) as input, and “returns” two parameters (by reference) as output. Parameters that are only used for returning values back to the caller are called **out parameters**. We’ve named these out parameters with the suffix “out” to denote that they’re out parameters. This helps remind the caller that the initial value passed to these parameters doesn’t matter, and that we should expect them to be rewritten. By convention, output parameters are typically the rightmost parameters.

If sin and cos had been passed by value instead of reference, getSinCos() would have changed copies of sin and cos, leading to any changes being discarded at the end of the function. But because sin and cos were passed by reference, any changes made to sin or cos (through the references) are persisted beyond the function. We can therefore use this mechanism to return values back to the caller.

This method, while functional, has a few minor downsides. First, the caller must pass in arguments to hold the updated outputs even if it doesn’t intend to use them. More importantly, the syntax is a bit unnatural, with both the input and output parameters being put together in the function call. It’s not obvious from the caller’s end that sin and cos are out parameters and will be changed. This is probably the most dangerous part of this method (as it can lead to mistakes being made). Some programmers and companies feel this is a big enough problem to advise avoiding output parameters altogether, or using pass by address for out parameters instead (which has a clearer syntax indicating whether a parameter is modifiable or not).

Personally, we recommend avoiding out parameters altogether if possible. If you do use them, naming out parameters (and output arguments) with an “out” suffix (or prefix) can help make it clear that the value might be modified.

**Limitations of pass by reference**

Non-const references can only reference non-const l-values (e.g. non-const variables), so a reference parameter cannot accept an argument that is a const l-value or an r-value (e.g. literals and the results of expressions).

Pass by const reference

As mentioned in the introduction, one of the major disadvantages of pass by value is that all arguments passed by value are copied into the function parameters. When the arguments are large structs or classes, this can take a lot of time. References provide a way to avoid this penalty. When an argument is passed by reference, a reference is created to the actual argument (which takes minimal time) and no copying of values takes place. This allows us to pass large structs and classes with a minimum performance penalty.

However, this also opens us up to potential trouble. References allow the function to change the value of the argument, which is undesirable when we want an argument be read-only. If we know that a function should not change the value of an argument, but don’t want to pass by value, the best solution is to pass by const reference.

You already know that a const reference is a reference that does not allow the variable being referenced to be changed through the reference. Consequently, if we use a const reference as a parameter, we guarantee to the caller that the function will not change the argument!

Using const is useful for several reasons:

- It enlists the compilers help in ensuring values that shouldn’t be changed aren’t changed (the compiler will throw an error if you try, like in the above example).
- It tells the programmer that the function won’t change the value of the argument. This can help with debugging.
- You can’t pass a const argument to a non-const reference parameter. Using const parameters ensures you can pass both non-const and const arguments to the function.
- Const references can accept any type of argument, including non-const l-values, const l-values, and r-values.

**Best practice**: When passing an argument by reference, always use a const reference unless you need to change the value of the argument.

**A reminder**: Non-const references cannot bind to r-values. A function with a non-const reference parameter cannot be called with literals or temporaries.

**References to pointers**

It’s possible to pass a pointer by reference, and have the function change the address of the pointer entirely:

```C++
#include <iostream>
 
void foo(int*& ptr) // pass pointer by reference
{
	ptr = nullptr; // this changes the actual ptr argument passed in, not a copy
}
 
int main()
{
	int x{ 5 };
	int *ptr{ &x };
	std::cout << "ptr is: " << (ptr ? "non-null" : "null") << '\n'; // prints non-null
	foo(ptr);
	std::cout << "ptr is: " << (ptr ? "non-null" : "null") << '\n'; // prints null
 
	return 0;
}
```

As a reminder, you can pass a C-style array by reference. This is useful if you need the ability for the function to change the array (e.g. for a sort function) or you need access to the array’s type information of a fixed array (to do sizeof() or a for-each loop). However, note that in order for this to work, you explicitly need to define the array size in the parameter:

```C++
#include <iostream>
 
// Note: You need to specify the array size in the function declaration
void printElements(int (&arr)[4])
{
  int length{ sizeof(arr) / sizeof(arr[0]) }; // we can now do this since the array won't decay
  
  for (int i{ 0 }; i < length; ++i)
  {
    std::cout << arr[i] << '\n';
  }
}
 
int main()
{
    int arr[]{ 99, 20, 14, 80 };
    
    printElements(arr);
 
    return 0;
}
```

This means this only works with fixed arrays of one particular length. If you want this to work with fixed arrays of any length, you can make the array length a template parameter (covered in a later chapter).

Pros and cons of pass by reference

Advantages of passing by reference:

- References allow a function to change the value of the argument, which is sometimes useful. Otherwise, const references can be used to guarantee the function won’t change the argument.
- Because a copy of the argument is not made, pass by reference is fast, even when used with large structs or classes.
- References can be used to return multiple values from a function (via out parameters).
- References must be initialized, so there’s no worry about null values.

Disadvantages of passing by reference:

- Because a non-const reference cannot be initialized with a const l-value or an r-value (e.g. a literal or an expression), arguments to non-const reference parameters must be normal variables.
- It can be hard to tell whether an argument passed by non-const reference is meant to be input, output, or both. Judicious use of const and a naming suffix for out variables can help.
- It’s impossible to tell from the function call whether the argument may change. An argument passed by value and passed by reference looks the same. We can only tell whether an argument is passed by value or reference by looking at the function declaration. This can lead to situations where the programmer does not realize a function will change the value of the argument.

When to use pass by reference:

- When passing structs or classes (use const if read-only).
- When you need the function to modify an argument.
- When you need access to the type information of a fixed array.

When not to use pass by reference:

- When passing fundamental types that don’t need to be modified (use pass by value).

**Best practice**: Use pass by (const) reference instead of pass by value for structs and classes and other expensive-to-copy types.

### 11.4 Passing Arguments by Address

There is one more way to pass variables to functions, and that is by address. **Passing an argument by address** involves passing the address of the argument variable rather than the argument variable itself. Because the argument is an address, the function parameter must be a pointer. The function can then dereference the pointer to access or change the value being pointed to.

**Addresses are actually passed by value**

When you pass a pointer to a function, the pointer’s value (the address it points to) is copied from the argument to the function’s parameter. In other words, it’s passed by value! If you change the function parameter’s value, you are only changing a copy. Consequently, the original pointer argument will not be changed.

Here’s a sample program that illustrates this.

```C++
#include <iostream>
 
void setToNull(int* tempPtr)
{
    // we're making tempPtr point at something else, not changing the value that tempPtr points to.
    tempPtr = nullptr; // use 0 instead if not C++11
}
 
int main()
{ 
    // First we set ptr to the address of five, which means *ptr = 5
    int five{ 5 };
    int* ptr{ &five };
	
    // This will print 5
    std::cout << *ptr;
 
    // tempPtr will receive a copy of ptr
    setToNull(ptr);
 
    // ptr is still set to the address of five!
 
    // This will print 5
    if (ptr)
        std::cout << *ptr;
    else
        std::cout << " ptr is null";
 
    return 0;
}
```

Note that even though the address itself is passed by value, you can still dereference that address to change the argument’s value. This is a common point of confusion, so let’s clarify:

- When passing an argument by address, the function parameter variable receives a copy of the address from the argument. At this point, the function parameter and the argument both point to the same value.
- If the function parameter is then *dereferenced* to change the value being pointed to, that *will* impact the value the argument is pointing to, since both the function parameter and argument are pointing to the same value!
- If the function parameter is *assigned* a different address, that **will not** impact the argument, since the function parameter is a copy, and changing the copy won’t impact the original. After changing the function parameter’s address, the function parameter and argument will point to different values, so dereferencing the parameter and changing the value will no longer affect the value pointed to by the argument.

**Passing addresses by reference**

The next logical question is, “What if we want to change the address an argument points to from within the function?”. Turns out, this is surprisingly easy. You can simply pass the address by reference. The syntax for doing a reference to a pointer is a little strange (and easy to get backwards). However, if you do get it backwards, the compiler will give you an error.

The following program illustrates using a reference to a pointer:

```C++
#include <iostream>
 
// tempPtr is now a reference to a pointer, so any changes made to tempPtr will change the argument as well!
void setToNull(int*& tempPtr)
{
    tempPtr = nullptr; // use 0 instead if not C++11
}
 
int main()
{ 
    // First we set ptr to the address of five, which means *ptr = 5
    int five{ 5 };
    int* ptr{ &five };
	
    // This will print 5
    std::cout << *ptr;
 
    // tempPtr is set as a reference to ptr
    setToNull(ptr);
 
    // ptr has now been changed to nullptr!
 
    if (ptr)
        std::cout << *ptr;
    else
        std::cout << " ptr is null";
 
    return 0;
}
```

**There is only pass by value**

Now that you understand the basic differences between passing by reference, address, and value, let’s get reductionist for a moment. :)

In the lesson [10.16 -- Reference variables], we briefly mentioned that references are typically implemented by the compiler as pointers. This means that behind the scenes, pass by reference is essentially just a pass by address (with access to the reference doing an implicit dereference).

And just above, we showed that pass by address is actually just passing an address by value!

Therefore, we can conclude that C++ really passes everything by value! The properties of pass by address (and reference) come *solely* from the fact that we can dereference the passed address to change the argument, which we can not do with a normal value parameter!

**Pass by address makes modifiable parameters explicit**

Consider the following example:

```C++
int foo1(int x); // pass by value
int foo2(int& x); // pass by reference
int foo3(int* x); // pass by address
 
int i {};
 
foo1(i);  // can't modify i
foo2(i);  // can modify i
foo3(&i); // can modify i
```

It’s not obvious from the call to foo2() that the function can modify variable i, is it?

For this reason, some guides recommend passing all modifiable arguments by address, so that it’s more obvious from an existing function call that an argument could be modified.

However, this comes with its own set of downsides: the caller might think they can pass in nullptr when they aren’t supposed to, and you now have to rigorously check for null pointers.

We lean towards the recommendation of passing non-optional modifiable parameters by reference. Even better, avoid modifiable parameters altogether.

**Pros and cons of pass by address**

Advantages of passing by address:

- Pass by address allows a function to change the value of the argument, which is sometimes useful. Otherwise, const can be used to guarantee the function won’t change the argument. (However, if you want to do this with a non-pointer, you should use pass by reference instead).
- Because a copy of the argument is not made, it is fast, even when used with large structs or classes.
- We can return multiple values from a function via out parameters.

Disadvantages of passing by address:

- Because literals (excepting C-style string literals) and expressions do not have addresses, pointer arguments must be normal variables.
- All values must be checked to see whether they are null. Trying to dereference a null value will result in a crash. It is easy to forget to do this.
- Because dereferencing a pointer is slower than accessing a value directly, accessing arguments passed by address is slower than accessing arguments passed by value.

When to use pass by address:

- When passing built-in arrays (if you’re okay with the fact that they’ll decay into a pointer).
- When passing a pointer and nullptr is a valid argument logically.

When not to use pass by address:

- When passing a pointer and nullptr is not a valid argument logically (use pass by reference).
- When passing structs or classes (use pass by reference).
- When passing fundamental types (use pass by value).

As you can see, pass by address and pass by reference have almost identical advantages and disadvantages. Because pass by reference is generally safer than pass by address, pass by reference should be preferred in most cases.

**Best practice**: Prefer pass by reference to pass by address whenever applicable.

### 11.5 Returning Values by Value, Reference and Address

As it turns out, returning values from a function to its caller by value, address, or reference works almost exactly the same way as passing arguments to a function does. All of the same upsides and downsides for each method are present. The primary difference between the two is simply that the direction of data flow is reversed. However, there is one more added bit of complexity -- because local variables in a function go out of scope and are destroyed when the function returns, we need to consider the effect of this on each return type.

**Return by value**

Return by value is the simplest and safest return type to use. When a value is returned by value, a copy of that value is returned to the caller. As with pass by value, you can return by value literals (e.g. 5), variables (e.g. x), or expressions (e.g. x+1), which makes return by value very flexible.

Another advantage of return by value is that you can return variables (or expressions) that involve local variables declared within the function without having to worry about scoping issues. Because the variables are evaluated before the function returns, and a copy of the value is returned to the caller, there are no problems when the function’s variable goes out of scope at the end of the function.

Return by value is the most appropriate when returning variables that were declared inside the function, or for returning function arguments that were passed by value. However, like pass by value, return by value is slow for structs and large classes.

When to use return by value:

- When returning variables that were declared inside the function
- When returning function arguments that were passed by value

When not to use return by value:

- When returning a built-in array or pointer (use return by address)
- When returning a large struct or class (use return by reference)

**Return by address**

Returning by address involves returning the address of a variable to the caller. Similar to pass by address, return by address can only return the address of a variable, not a literal or an expression (which don’t have addresses). Because return by address just copies an address from the function to the caller, return by address is fast.

However, return by address has one additional downside that return by value doesn’t -- if you try to return the address of a variable local to the function, your program will exhibit undefined behavior. Consider the following example:

```C++
int* doubleValue(int x)
{
    int value{ x * 2 };
    return &value; // return value by address here
} // value destroyed here
```

As you can see here, value is destroyed just after its address is returned to the caller. The end result is that the caller ends up with the address of non-allocated memory (a dangling pointer), which will cause problems if used.

Return by address was often used to return dynamically allocated memory to the caller:

```C++
int* allocateArray(int size)
{
    return new int[size];
}
 
int main()
{
    int* array{ allocateArray(25) };
 
    // do stuff with array
 
    delete[] array;
    return 0;
}
```

This works because dynamically allocated memory is not destroyed at the end of the block in which it is allocated, so that memory will still exist when the address is returned back to the caller. Keeping track of manual allocations can be difficult. Separating the allocation and deletion into different functions makes it even harder to understand who’s responsible for deleting the resource or if the resource needs to be deleted at all. Smart pointers (covered later) and types that clean up after themselves should be used instead of manual allocations.

When to use return by address:

- When returning dynamically allocated memory and you can’t use a type that handles allocations for you
- When returning function arguments that were passed by address

When not to use return by address:

- When returning variables that were declared inside the function or parameters that were passed by value (use return by value)
- When returning a large struct or class that was passed by reference (use return by reference)

**Return by reference**

Similar to return by address, values returned by reference must be variables (you should not return a reference to a literal or an expression that resolves to a temporary value, as those will go out of scope at the end of the function and you’ll end up returning a dangling reference). When a variable is returned by reference, a reference to the variable is passed back to the caller. The caller can then use this reference to continue modifying the variable, which can be useful at times. Return by reference is also fast, which can be useful when returning structs and classes.

However, just like return by address, you should not return local variables by reference. Consider the following example:

```C++
int& doubleValue(int x)
{
    int value{ x * 2 };
    return value; // return a reference to value here
} // value is destroyed here
```

When to use return by reference:

- When returning a reference parameter
- When returning a member of an object that was passed into the function by reference or address
- When returning a large struct or class that will not be destroyed at the end of the function (e.g. one that was passed in by reference)

When not to use return by reference:

- When returning variables that were declared inside the function or parameters that were passed by value (use return by value)
- When returning a built-in array or pointer value (use return by address)

### 11.6 Inline Functions

The use of functions provides many benefits, including:

- The code inside the function can be reused.
- It is much easier to change or update the code in a function (which needs to be done once) than for every in-place instance. Duplicate code is a recipe for inefficiency and errors.
- It makes your code easier to read and understand, as you do not have to know how a function is implemented to understand what it does (assuming responsible function naming or comments).
- Functions provide type checking to ensure function call arguments match the function parameters (function-like macros don’t do this, which can lead to errors).
- Functions make your program easier to debug.

However, one major downside of functions is that every time a function is called, there is a certain amount of performance overhead that occurs. This is because the CPU must store the address of the current instruction it is executing (so it knows where to return to later) along with other registers, all the function parameters must be created and assigned values, and the program has to branch to a new location. Code written in-place is significantly faster.

For functions that are large and/or perform complex tasks, the overhead of the function call is usually insignificant compared to the amount of time the function takes to run. However, for small, commonly-used functions, the time needed to make the function call is often a lot more than the time needed to actually execute the function’s code. This can result in a substantial performance penalty.

C++ offers a way to combine the advantages of functions with the speed of code written in-place: inline functions. The **inline** keyword is used to request that the compiler treat your function as an inline function. When the compiler compiles your code, all inline functions are expanded in-place -- that is, the function call is replaced with a copy of the contents of the function itself, which removes the function call overhead! The downside is that because the inline function is expanded in-place for *every* function call, this can make your compiled code quite a bit larger, especially if the inline function is long and/or there are many calls to the inline function.

Because of the potential for code bloat, inlining a function is best suited to short functions (e.g. no more than a few lines) that are typically called inside loops and do not branch. Also note that the inline keyword is only a recommendation -- the compiler is free to ignore your request to inline a function. This is likely to be the result if you try to inline a lengthy function!

Finally, modern compilers are now very good at inlining functions automatically -- better than humans in most cases. Even if you don’t mark a function as inline, the compiler will inline functions that it believes will result in performance increases. Thus, in most cases, there isn’t a specific need to use the inline keyword. Let the compiler handle inlining functions for you.

**Best practice**: Be aware of inline functions, but modern compilers should inline functions for you as appropriate, so there isn’t a need to use the inline keyword in this context.

**Inline functions are exempt from the one-definition per program rule**

In previous chapters, we’ve noted that you should not implement functions (with external linkage) in header files, because when those headers are included into multiple .cpp files, the function definition will be copied into multiple .cpp files. These files will then be compiled, and the linker will throw an error because it will note that you’ve defined the same function more than once.

However, inline functions are exempt from the rule that you can only have one definition per program, because of the fact that inline functions do not actually result in a real function being compiled -- therefore, there’s no conflict when the linker goes to link multiple files together.

This may seem like an uninteresting bit of trivia at this point, but next chapter we’ll introduce a new type of function (a member function) that makes significant use of this point.

Even with inline functions, you generally should not define global functions in header files.

### 11.7 Function Pointers

A **pointer** is a variable that holds the address of another variable. Function pointers are similar, except that instead of pointing to variables, they point to functions!

**Pointers to functions**

The syntax for creating a non-const function pointer is one of the ugliest things you will ever see in C++:

```C++
// fcnPtr is a pointer to a function that takes no arguments and returns an integer
int (*fcnPtr)();
```

In the above snippet, fcnPtr is a pointer to a function that has no parameters and returns an integer. fcnPtr can point to any function that matches this type.

The parenthesis around *fcnPtr are necessary for precedence reasons, as `int *fcnPtr()` would be interpreted as a forward declaration for a function named fcnPtr that takes no parameters and returns a pointer to an integer.

To make a const function pointer, the const goes after the asterisk:

```C++
int (*const fcnPtr)();
```

If you put the const before the int, then that would indicate the function being pointed to would return a const int.

**Assigning a function to a function pointer**

Function pointers can be initialized with a function (and non-const function pointers can be assigned a function).

Note that the type (parameters and return type) of the function pointer must match the type of the function.

Here are some examples of this:

```C++
// function prototypes
int foo();
double goo();
int hoo(int x);
 
// function pointer assignments
int (*fcnPtr1)(){ &foo }; // okay
int (*fcnPtr2)(){ &goo }; // wrong -- return types don't match!
double (*fcnPtr4)(){ &goo }; // okay
fcnPtr1 = &hoo; // wrong -- fcnPtr1 has no parameters, but hoo() does
int (*fcnPtr3)(int){ &hoo }; // okay
```

Unlike fundamental types, C++ *will* implicitly convert a function into a function pointer if needed (so you don’t need to use the address-of operator (&) to get the function’s address). However, it will not implicitly convert function pointers to void pointers, or vice-versa.

**Calling a function using a function pointer**

The other primary thing you can do with a function pointer is use it to actually call the function. There are two ways to do this. The first is via explicit dereference:

```C++
int foo(int x)
{
    return x;
}
 
int main()
{
    int (*fcnPtr)(int){ &foo }; // Initialize fcnPtr with function foo
    (*fcnPtr)(5); // call function foo(5) through fcnPtr.
 
    return 0;
}
```

The second way is via implicit dereference:

```C++
int foo(int x)
{
    return x;
}
 
int main()
{
    int (*fcnPtr)(int){ &foo }; // Initialize fcnPtr with function foo
    fcnPtr(5); // call function foo(5) through fcnPtr.
 
    return 0;
}
```

As you can see, the implicit dereference method looks just like a normal function call -- which is what you’d expect, since normal function names are pointers to functions anyway! However, some older compilers do not support the implicit dereference method, but all modern compilers should.

One interesting note: Default parameters won’t work for functions called through function pointers. Default parameters are resolved at compile-time (that is, if you don’t supply an argument for a defaulted parameter, the compiler substitutes one in for you when the code is compiled). However, function pointers are resolved at run-time. Consequently, default parameters can not be resolved when making a function call with a function pointer. You’ll explicitly have to pass in values for any defaulted parameters in this case.

**Passing functions as arguments to other functions**

One of the most useful things to do with function pointers is pass a function as an argument to another function. Functions used as arguments to another function are sometimes called **callback functions**.

Consider a case where you are writing a function to perform a task (such as sorting an array), but you want the user to be able to define how a particular part of that task will be performed (such as whether the array is sorted in ascending or descending order). Let’s take a closer look at this problem as applied specifically to sorting, as an example that can be generalized to other similar problems.

Many comparison-based sorting algorithms work on a similar concept: the sorting algorithm iterates through a list of numbers, does comparisons on pairs of numbers, and reorders the numbers based on the results of those comparisons. Consequently, by varying the comparison, we can change the way the algorithm sorts without affecting the rest of the sorting code.

