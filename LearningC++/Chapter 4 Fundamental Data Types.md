## Chapter 4 Fundamental Data Types

we talked about the fact that variables are names for a piece of memory that can be used to store information. The smallest unit of memory is a **binary digit** (also called a **bit**), which can hold a value of 0 or 1. Memory is organized into sequential units called **memory addresses** (or **addresses** for short). Similar to how a street address can be used to find a given house on a street, the memory address allows us to find and access the contents of memory at a particular location.

Perhaps surprisingly, in modern computer architectures, each bit does not get its own unique memory address. This is because the number of memory addresses are limited, and the need to access data bit-by-bit is rare. Instead, each memory address holds 1 byte of data. A **byte** is a group of bits that are operated on as a unit. The modern standard is that a byte is comprised of 8 sequential bits.

**Data types**

Because all data on a computer is just a sequence of bits, we use a **data type** (often called a “type” for short) to tell the compiler how to interpret the contents of memory in some meaningful way. You have already seen one example of a data type: the integer. When we declare a variable as an integer, we are telling the compiler “the piece of memory that this variable uses is going to be interpreted as an integer value”.

When you give an object a value, the compiler and CPU take care of encoding your value into the appropriate sequence of bits for that data type, which are then stored in memory (remember: memory can only store bits). For example, if you assign an integer object the value *65*, that value is converted to the sequence of bits `0100 0001` and stored in the memory assigned to the object.

Conversely, when the object is evaluated to produce a value, that sequence of bits is reconstituted back into the original value. Meaning that `0100 0001` is converted back into the value *65*.

Fortunately, the compiler and CPU do all the hard work here, so you generally don’t need to worry about how values gets converted into bit sequences and back.

All you need to do is pick a data type for your object that best matches your desired use.

**The _t suffix**

Many of the types defined in newer versions of C++ (e.g. std::nullptr_t) use a _t suffix. This suffix means “type”, and it’s a common nomenclature applied to modern types.

If you see something with a _t suffix, it’s probably a type. But many types don’t have a _t suffix, so this isn’t consistently applied.

**Best Practice**: Use an empty parameter list instead of void to indicate that a function has no paramenters.

void means no type. It has two usages: the first is used as function return value, the other is used as void pointer.

The sizeof operator

We can use the sizeof operator to display the byte size of a fundamental data type. Here is an example:

```C++
#include <iostream>
 
int main()
{
    std::cout << "bool:\t\t" << sizeof(bool) << " bytes\n";
    std::cout << "char:\t\t" << sizeof(char) << " bytes\n";
    std::cout << "wchar_t:\t" << sizeof(wchar_t) << " bytes\n";
    std::cout << "char16_t:\t" << sizeof(char16_t) << " bytes\n";
    std::cout << "char32_t:\t" << sizeof(char32_t) << " bytes\n";
    std::cout << "short:\t\t" << sizeof(short) << " bytes\n";
    std::cout << "int:\t\t" << sizeof(int) << " bytes\n";
    std::cout << "long:\t\t" << sizeof(long) << " bytes\n";
    std::cout << "long long:\t" << sizeof(long long) << " bytes\n";
    std::cout << "float:\t\t" << sizeof(float) << " bytes\n";
    std::cout << "double:\t\t" << sizeof(double) << " bytes\n";
    std::cout << "long double:\t" << sizeof(long double) << " bytes\n";
 
    return 0;
}
```

The output is:

<img src="D:\Study\学习笔记\Images\type_sizeof.png" style="zoom:80%;" />

By default, integers are **signed**, which means the number’s sign is stored as part of the number (using a single bit called the **sign bit**). Therefore, a signed integer can hold both positive and negative numbers (and 0).

**Integer overflow** (often called *overflow* for short) occurs when we try to store a value that is outside the range of the type. Essentially, the number we are trying to store requires more bits to represent than the object has available. In such a case, data is lost because the object doesn’t have enough memory to store everything.

**Unsigned integers** are integers that can only hold non-negative whole numbers.

**Warning**: The fixed-width integers should be avoided, as they may not be defined on all target architectures.

 To help address the above downsides, C++ also defines two alternative sets of integers.

The fast type (std::int_fast#_t) provides the fastest signed integer type with a width of at least # bits (where # = 8, 16, 32, or 64). For example, *std::int_fast32_t* will give you the fastest signed integer type that’s at least 32 bits.

The least type (std::int_least#_t) provides the smallest signed integer type with a width of at least # bits (where # = 8, 16, 32, or 64). For example, *std::int_least32_t* will give you the smallest signed integer type that’s at least 32 bits.

There is also an unsigned set of fast and least types (std::uint_fast#_t and std::uint_least#_t).

These fast and least types are guaranteed to be defined, and are safe to use.

Best practice

---

Favor the std::int_fast#_t and std::int_least#_t integers when you need an integer guaranteed to be at least a certain minimum size.

**std::size_t** is defined as an unsigned integral type, and it is typically used to represent the size or length of objects. Much like an integer can vary in size depending on the system, *std::size_t* also varies in size. *std::size_t* is guaranteed to be unsigned and at least 16 bits, but on most systems will be equivalent to the address-width of the application. That is, for 32-bit applications, *std::size_t* will typically be a 32-bit unsigned integer, and for a 64-bit application, *size_t* will typically be a 64-bit unsigned integer. *size_t* is defined to be big enough to hold the size of the largest object creatable on your system (in bytes). 

**Scientific notation** is a useful shorthand for writing lengthy numbers in a concise manner.

By convention, numbers in scientific notation are written with one digit before the decimal point, and the rest of the digits afterward. Because it can be hard to type or display exponents in C++, we use the letter ‘e’ (or sometimes ‘E’) to represent the “times 10 to the power of” part of the equation. For example, `1.2 x 10^4` would be written as `1.2e4`, and `5.9736 x 10^24` would be written as `5.9736e24`.

A **floating point** type variable is a variable that can hold a real number, such as 4320.0, -3.33, or 0.01226. The *floating* part of the name *floating point* refers to the fact that the decimal point can “float”; that is, it can support a variable number of digits before and after the decimal point.

There are three different floating point data types: **float**, **double**, and **long double**. As with integers, C++ does not define the actual size of these types (but it does guarantee minimum sizes). On modern architectures, floating point representation almost always follows IEEE 754 binary format. In this format, a float is 4 bytes, a double is 8, and a long double can be equivalent to a double (8 bytes), 80-bits (often padded to 12 bytes), or 16 bytes.

Floating point data types are always signed (can hold positive and negative values).

The **precision** of a floating point number defines how many *significant digits* it can represent without information loss.

When outputting floating point numbers, std::cout has a default precision of 6 -- that is, it assumes all floating point variables are only significant to 6 digits (the minimum precision of a float), and hence it will truncate anything after that.

We can override the default precision that std::cout shows by using an `output manipulator` function named `std::setprecision()`. **Output manipulators** alter how data is output, and are defined in the *iomanip* header.

```c++
#include <iostream>
#include <iomanip> // for output manipulator std::setprecision()
 
int main()
{
    std::cout << std::setprecision(16); // show 16 digits of precision
    std::cout << 3.33333333333333333333333333333333333333f <<'\n'; // f suffix means float
    std::cout << 3.33333333333333333333333333333333333333 << '\n'; // no suffix means double
    return 0;
}
```

Outputs:

`3.333333253860474`

`3.333333333333334`

When precision is lost because a number can’t be stored precisely, this is called a **rounding error**.

If you want std::cout to print “true” or “false” instead of 0 or 1, you can use *std::boolalpha*. You can use *std::noboolalpha* to turn it back off.

**Key Insight**: Whenever you see C++ syntax (excluding the preprocessor) that makes use of angled brackets, the thing between the angled brackets will most likely be a type. This is typically how C++ deals with concepts that need a parameterizable type.

In C++, we use strings to represent text such as names, addresses, words, and sentences. String literals (such as “Hello, world!\n”) are placed between double quotes to identify them as strings.

**`std::string`**

In C++, strings aren’t a fundamental type (they’re actually a `compound type`, and defined in the C++ standard library rather than as part of the core language).

To use strings in C++, we first need to #include the <string> header to bring in the declarations for std::string. Once that is done, we can define variables of type std::string.

In programming, a **constant** is a fixed value that may not be changed. C++ has two kinds of constants: literal constants, and symbolic constants.

**Literal constants** (usually just called **literals**) are values inserted directly into the code.

They are constants because their values can not be changed dynamically (you have to change them, and then recompile for the change to take effect).

**Best Practice**: Don't use magic number in your code.

To make a variable constant, simply put the `const` keyword either before or after the variable type. Const variables *must* be initialized when you define them, and then that value can not be changed via assignment. Note that const variables can be initialized from other variables (including non-const ones).

When arguments are passed by value, we generally don’t care if the function changes the value of the parameter (since it’s just a copy that will be destroyed at the end of the function anyway). For this reason, we usually don’t const parameters passed by value. But later on, we’ll talk about other kinds of function parameters (where changing the value of the parameter will change the value of the argument passed in). For these other types of parameters, use of const is important.

**Runtime vs. Compile-time Constants**

C++ actually has two different kinds of constants.

- **Runtime constants** are constants whose initialization values can only be resolved at runtime (when your program is running). Here's some examples:

    ```C++
    #include <iostream>
     
    void printInt(const int x) // x is a runtime constant because the value isn't known until the program is run
    {
        std::cout << x;
    }
     
    int main()
    {
        std::cout << "Enter your age: ";
        int age{};
        std::cin >> age;
     
        const int usersAge { age }; // usersAge is a runtime constant because the value isn't known until the program is run
     
        std::cout << "Your age is: ";
        printInt(age);
     
        return 0;
    }
    ```

    Variables such as *usersAge* and *x* in the above program above are runtime constants, because the compiler can’t determine their initial values until the program is actually run. *usersAge* relies on user input (which can only be given at runtime) and *x* depends on the value passed into the function (which is only known at runtime). However, once initialized, the value of these constants can’t be changed.

- **Compile-time constants** are constants whose initialization values can be determined at compile-time (when your program is compiling). The following are examples of compile-time constants:

    ```C++
    const double gravity { 9.8 }; // the compiler knows at compile-time that gravity will have value 9.8
    const int something { 1 + 2 }; // the compiler can resolve this at compiler time
    ```

    Compile-time constants enable the compiler to perform optimizations that aren’t available with runtime constants. For example, whenever *gravity* is used, the compiler can simply substitute the identifier *gravity* with the literal double *9.8*.

When you declare a const variable, the compiler will implicitly keep track of whether it’s a runtime or compile-time constant. In most cases, this doesn’t matter, but there are a few odd cases where C++ requires a compile-time constant instead of a run-time constant

**constexpr**

To help provide more specificity, C++11 introduced the keyword **constexpr**, which ensures that a constant must be a compile-time constant:

```C++
#include <iostream>
 
int main()
{
    constexpr double gravity { 9.8 }; // ok, the value of 9.8 can be resolved at compile-time
    constexpr int sum { 4 + 5 }; // ok, the value of 4 + 5 can be resolved at compile-time
 
    std::cout << "Enter your age: ";
    int age{};
    std::cin >> age;
 
    constexpr int myAge { age }; // compile error: age is a runtime constant, not a compile-time constant
 
    return 0;
}
```

**Best Practice**: Any variable that should not be modifiable after initialization and whose initializer is known at compile-time should be declared as constexpr.
Any variable that should not be modifiable after initialization and whose initializer is not known at compile-time should be declared as const.

A **symbolic constant** is a name given to a constant literal value. There are two ways to declare symbolic constants in C++. One of them is good, and one of them is not. We’ll show you both.