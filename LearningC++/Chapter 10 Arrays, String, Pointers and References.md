# Chapter 10 Arrays, String, Pointers and References

Note: This chapter is a bit harder than the previous ones. If you feel a little discouraged, stick with it. The best stuff is yet to come!

### 10.1 Arrays(Part 1)

An **array** is an aggregate data type that lets us access many variables of the same type through a single identifier.

In an array variable declaration, we use square brackets ([]) to tell the compiler both that this is an array variable (instead of a normal variable), as well as how many variables to allocate (called the **array length**).

A **fixed array** (also called a **fixed length array** or **fixed size array**) is an array where the length is known at compile time. 

**Array elements and subscripting**

Each of the variables in an array is called an **element**. Elements do not have their own unique names. Instead, to access individual elements of an array, we use the array name, along with the **subscript operator ([])**, and a parameter called a **subscript** (or **index**) that tells the compiler which element we want. This process is called **subscripting** or **indexing** the array.

For an array of length N, the array elements are numbered 0 through N-1. This is called the array’s **range**.

**Array data types**

Arrays can be made from any data type. Arrays can also be made from structs. 

**Array subscripts**

In C++, array subscripts must always be an integral type. This includes char, short, int, long, long long, etc… and strangely enough, bool (where false gives an index of 0 and true gives an index of 1). An array subscript can be a literal value, a variable (constant or non-constant), or an expression that evaluates to an integral type.

**Fixed array declarations**

When declaring a fixed array, the length of the array (between the square brackets) must be a compile-time constant. This is because the length of a fixed array must be known at compile time. Here are some different ways to declare fixed arrays:

```C++
// using a literal constant
int numberOfLessonsPerDay[7]{}; // Ok
 
// using a constexpr symbolic constant
constexpr int daysPerWeek{ 7 };
int numberOfLessonsPerDay[daysPerWeek]{}; // Ok
 
// using an enumerator
enum Weekday
{
    monday,
    tuesday,
    wednesday,
    thursday,
    friday,
    saturday,
    sunday,
    
    maxWeekday
};
int numberOfLessonsPerDay[maxWeekday]{}; // Ok
 
// using a macro
#define DAYS_PER_WEEK 7
int numberOfLessonsPerDay[DAYS_PER_WEEK]{}; // Works, but don't do this (use a constexpr symbolic constant instead)
```

Note that non-const variables or runtime constants cannot be used:

```C++
// using a non-const variable
int daysPerWeek{};
std::cin >> daysPerWeek;
int numberOfLessonsPerDay[daysPerWeek]{}; // Not ok -- daysPerWeek is not a compile-time constant!
 
// using a runtime const variable
int temp{ 5 };
const int daysPerWeek{ temp }; // the value of daysPerWeek isn't known until runtime, so this is a runtime constant, not a compile-time constant!
int numberOfLessonsPerDay[daysPerWeek]{}; // Not ok
```

Note that in the last two cases, an error should result because the length is not a compile-time constant. Some compilers may allow these kinds of arrays (for C99 compatibility reasons), but they are invalid in C++, and should not be used in C++ programs. If your compiler allows these arrays, you probably forgot to disable compiler extensions.

**A note on dynamic arrays**

Because fixed arrays have memory allocated at compile time, that introduces two limitations:

- Fixed arrays cannot have a length based on either user input or some other value calculated at runtime.
- Fixed arrays have a fixed length that can not be changed.

In many cases, these limitations are problematic. Fortunately, C++ supports a second kind of array known as a **dynamic array**. The length of a dynamic array can be set at runtime, and their length can be changed. However, dynamic arrays are a little more complicated to instantiate, so we’ll cover them later in the chapter.

**Summary**

Fixed arrays provide an easy way to allocate and use multiple variables of the same type so long as the length of the array is known at compile time.

### 10.2 Arrays(Part 2)

**Initializing fixed arrays**

Array elements are treated just like normal variables, and as such, they are not initialized when created.

One way to “initialize” an array is to do it element by element:

```C++
int prime[5]; // hold the first 5 prime numbers
prime[0] = 2;
prime[1] = 3;
prime[2] = 5;
prime[3] = 7;
prime[4] = 11;
```

However, this is a pain, especially as the array gets larger. Furthermore, it’s not initialization, but assignment. Assignments don’t work if the array is `const`.

Fortunately, C++ provides a more convenient way to initialize entire arrays via use of an **initializer list**. The following example initializes the array with the same values as the one above:

```C++
int prime[5]{ 2, 3, 5, 7, 11 }; // use initializer list to initialize the fixed array
```

If there are more initializers in the list than the array can hold, the compiler will generate an error. However, if there are less initializers in the list than the array can hold, the remaining elements are initialized to 0 (or whatever value 0 converts to for a non-integral fundamental type -- e.g. 0.0 for double). This is called **zero initialization**. Consequently, to initialize all the elements of an array to 0, you can do this:

```C++
// Initialize all elements to 0
int array[5]{ };
 
// Initialize all elements to 0.0
double array[5]{ };
 
// Initialize all elements to an empty string
std::string array[5]{ };
```

If the initializer list is omitted, the elements are uninitialized, unless they are a class-type.

```C++
// uninitialized
int array[5];
 
// uninitialized
double array[5];
 
// Initialize all elements to an empty string
std::string array[5];
```

**Best practice**: Explicitly initialize arrays, even if they would be initialized without an initializer list.

**Omitted length**

If you are initializing a fixed array of elements using an initializer list, the compiler can figure out the length of the array for you, and you can omit explicitly declaring the length of the array.

The following two lines are equivalent:

```C++
int array[5]{ 0, 1, 2, 3, 4 }; // explicitly define the length of the array
int array[]{ 0, 1, 2, 3, 4 }; // let the initializer list set length of the array
```

This not only saves typing, it also means you don’t have to update the array length if you add or remove elements later.

**Arrays and enums**

One of the big documentation problems with arrays is that integer indices do not provide any information to the programmer about the meaning of the index. Consider a class of 5 students:

```C++
constexpr int numberOfStudents{5};
int testScores[numberOfStudents]{};
testScores[2] = 76;
```

Who is represented by testScores[2]? It’s not clear.

This can be solved by setting up an enumeration where one enumerator maps to each of the possible array indices:

```C++
enum StudentNames
{
    kenny, // 0
    kyle, // 1
    stan, // 2
    butters, // 3
    cartman, // 4
    max_students // 5
};
 
int main()
{
    int testScores[max_students]{}; // allocate 5 integers
    testScores[stan] = 76;
 
    return 0;
}
```

In this way, it’s much clearer what each of the array elements represents. Note that an extra enumerator named max_students has been added. This enumerator is used during the array declaration to ensure the array has the proper length (as the array length should be one greater than the largest index). This is useful both for documentation purposes, and because the array will automatically be resized if another enumerator is added.

Note that this “trick” only works if you do not change the enumerator values manually!

**Arrays and enum classes**

Enum classes don’t have an implicit conversion to integer, so if you try the following:

```C++
enum class StudentNames
{
    kenny, // 0
    kyle, // 1
    stan, // 2
    butters, // 3
    cartman, // 4
    wendy, // 5
    max_students // 6
};
 
int main()
{
    int testScores[StudentNames::max_students]{}; // allocate 6 integers
    testScores[StudentNames::stan] = 76;
 
    return 0;
}
```

You’ll get a compiler error. This can be addressed by using a static_cast to convert the enumerator to an integer:

```C++
int main()
{
    int testScores[static_cast<int>(StudentNames::max_students)]{}; // allocate 6 integers
    testScores[static_cast<int>(StudentNames::stan)] = 76;
 
    return 0;
}
```

However, doing this is somewhat of a pain, so it might be better to use a standard enum inside of a namespace:

```C++
namespace StudentNames
{
    enum StudentNames
    {
        kenny, // 0
        kyle, // 1
        stan, // 2
        butters, // 3
        cartman, // 4
        wendy, // 5
        max_students // 6
    };
}
 
int main()
{
    int testScores[StudentNames::max_students]{}; // allocate 6 integers
    testScores[StudentNames::stan] = 76;
 
    return 0;
}
```

**Passing arrays to functions**

Although passing an array to a function at first glance looks just like passing a normal variable, underneath the hood, C++ treats arrays differently.

When a normal variable is passed by value, C++ copies the value of the argument into the function parameter. Because the parameter is a copy, changing the value of the parameter does not change the value of the original argument.

However, because copying large arrays can be very expensive, C++ does *not* copy an array when an array is passed into a function. Instead, the *actual* array is passed. This has the side effect of allowing functions to directly change the value of array elements!

**Determining the length of an array**

The std::size() function from the <iterator> header can be used to determine the length of arrays.

std::size() will work with other kinds of objects (such as std::array and std::vector), and it will cause a compiler error if you try to use it on a fixed array that has been passed to a function! Note that std::size returns an unsigned value. If you need a signed value, you can either cast the result or, since C++20, use std::ssize() (Stands for signed size).

std::size() was added in C++17. If you’re still using an old compiler, you have to use the sizeof operator instead. sizeof isn’t as easy to use as std::size() and there are a few things you have to watch out for. If you’re using a C++17-capable compiler, you can skip to section “Indexing an array out of range”.

The sizeof operator can be used on arrays, and it will return the total size of the array (array length multiplied by element size).

When sizeof is used on an array that has been passed to a function, it doesn’t error out like std::size() does. Instead, it returns the size of a pointer.

**Indexing an array out of range**

Remember that an array of length N has array elements 0 through N-1. So what happens if you try to access an array with a subscript outside of that range?

Consider the following program:

```C++
int main()
{
    int prime[5]{}; // hold the first 5 prime numbers
    prime[5] = 13;
 
    return 0;
}
```

In this program, our array is of length 5, but we’re trying to write a prime number into the 6th element (index 5).

C++ does *not* do any checking to make sure that your indices are valid for the length of your array. So in the above example, the value of 13 will be inserted into memory where the 6th element would have been had it existed. When this happens, you will get undefined behavior -- For example, this could overwrite the value of another variable, or cause your program to crash.

Although it happens less often, C++ will also let you use a negative index, with similarly undesirable results.

### 10.3 Arrays and Loops

Mixing loops and arrays

Loops are typically used with arrays to do one of three things:
\(1) Calculate a value (e.g. average value, total value)
\(2) Search for a value (e.g. highest value, lowest value).
\(3) Reorganize the array (e.g. ascending order, descending order)

Arrays and off-by-one errors

One of the trickiest parts of using loops with arrays is making sure the loop iterates the proper number of times. Off-by-one errors are easy to make, and trying to access an element that is larger than the length of the array can have dire consequences. 

Consequently, when using loops with arrays, always double-check your loop conditions to make sure you do not introduce off-by-one errors.

### 10.4 Sorting an Array by Using selection Sort

Sorting an array is the process of arranging all of the elements in the array in a particular order. 

**How sorting works**

Sorting is generally performed by repeatedly comparing pairs of array elements, and swapping them if they meet some predefined criteria. The order in which these elements are compared differs depending on which sorting algorithm is used. The criteria depends on how the list will be sorted (e.g. in ascending or descending order).

To swap two elements, we can use the std::swap() function from the C++ standard library, which is defined in the utility header.

**Selection sort**

There are many ways to sort an array. Selection sort is probably the easiest sort to understand, which makes it a good candidate for teaching even though it is one of the slower sorts.

Selection sort performs the following steps to sort an array from smallest to largest:
\(1) Starting at array index 0, search the entire array to find the smallest value
\(2) Swap the smallest value found in the array with the value at index 0
\(3) Repeat steps 1 & 2 starting from the next index

In other words, we’re going to find the smallest element in the array, and swap it into the first position. Then we’re going to find the next smallest element, and swap it into the second position. This process will be repeated until we run out of elements.

**Selection sort in C++**

Here’s how this algorithm is implemented in C++:

```C++
#include <iostream>
#include <iterator>
#include <utility>
 
int main()
{
	int array[]{ 30, 50, 20, 10, 40 };
	constexpr int length{ static_cast<int>(std::size(array)) };
 
	// Step through each element of the array
	// (except the last one, which will already be sorted by the time we get there)
	for (int startIndex{ 0 }; startIndex < length - 1; ++startIndex)
	{
		// smallestIndex is the index of the smallest element we’ve encountered this iteration
		// Start by assuming the smallest element is the first element of this iteration
		int smallestIndex{ startIndex };
 
		// Then look for a smaller element in the rest of the array
		for (int currentIndex{ startIndex + 1 }; currentIndex < length; ++currentIndex)
		{
			// If we've found an element that is smaller than our previously found smallest
			if (array[currentIndex] < array[smallestIndex])
				// then keep track of it
				smallestIndex = currentIndex;
		}
 
		// smallestIndex is now the smallest element in the remaining array
                // swap our start element with our smallest element (this sorts it into the correct place)
		std::swap(array[startIndex], array[smallestIndex]);
	}
 
	// Now that the whole array is sorted, print our sorted array as proof it works
	for (int index{ 0 }; index < length; ++index)
		std::cout << array[index] << ' ';
 
	std::cout << '\n';
 
	return 0;
}
```

Sorting names works using the same algorithm. Just change the array type from int to std::string, and initialize with the appropriate values.

`std::sort`

Because sorting arrays is so common, the C++ standard library includes a sorting function named `std::sort`. `std::sort` lives in the <algorithm> header, and can be invoked on an array like so:

```C++
#include <algorithm> // for std::sort#include <iostream>#include <iterator> // for std::size int main(){	int array[]{ 30, 50, 20, 10, 40 }; 	std::sort(std::begin(array), std::end(array)); 	for (int i{ 0 }; i < static_cast<int>(std::size(array)); ++i)		std::cout << array[i] << ' '; 	std::cout << '\n'; 	return 0;}
```

By default, std::sort sorts in ascending order using operator< to compare pairs of elements and swapping them if necessary (much like our selection sort example does above).

### 10.5 Multidimensional Arrays

The elements of an array can be of any data type, including arrays! An array of arrays is called a **multidimensional array**.

```C++
int array[3][5]; // a 3-element array of 5-element arrays
```

Since we have 2 subscripts, this is a two-dimensional array.

In a two-dimensional array, it is convenient to think of the first (left) subscript as being the row, and the second (right) subscript as being the column. This is called **row-major** order. Conceptually, the above two-dimensional array is laid out as follows:

```
[0][0]  [0][1]  [0][2]  [0][3]  [0][4] // row 0
[1][0]  [1][1]  [1][2]  [1][3]  [1][4] // row 1
[2][0]  [2][1]  [2][2]  [2][3]  [2][4] // row 2
```

**Initializing two-dimensional arrays**

To initialize a two-dimensional array, it is easiest to use nested braces, with each set of numbers representing a row:

```C++
int array[3][5]
{
  { 1, 2, 3, 4, 5 }, // row 0
  { 6, 7, 8, 9, 10 }, // row 1
  { 11, 12, 13, 14, 15 } // row 2
};
```

Although some compilers will let you omit the inner braces, we highly recommend you include them anyway, both for readability purposes and because of the way that C++ will replace missing initializers with 0.

```C++
int array[3][5]{  { 1, 2 }, // row 0 = 1, 2, 0, 0, 0  { 6, 7, 8 }, // row 1 = 6, 7, 8, 0, 0  { 11, 12, 13, 14 } // row 2 = 11, 12, 13, 14, 0};
```

Two-dimensional arrays with initializer lists can omit (only) the leftmost length specification:

```C++
int array[][5]
{
  { 1, 2, 3, 4, 5 },
  { 6, 7, 8, 9, 10 },
  { 11, 12, 13, 14, 15 }
};
```

Just like normal arrays, multidimensional arrays can still be initialized to 0 as follows:

```C++
int array[3][5]{};
```

**Accessing elements in a two-dimensional array**

Accessing all of the elements of a two-dimensional array requires two loops: one for the row, and one for the column. Since two-dimensional arrays are typically accessed row by row, the row index is typically used as the outer loop.

**Multidimensional arrays larger than two dimensions**

Multidimensional arrays may be larger than two dimensions. Here is a declaration of a three-dimensional array:

```C++
int array[5][4][3];
```

Three-dimensional arrays are hard to initialize in any kind of intuitive way using initializer lists, so it’s typically better to initialize the array to 0 and explicitly assign values using nested loops.

Accessing the element of a three-dimensional array is analogous to the two-dimensional case:

```C++
std::cout << array[3][1][2];
```

Two dimensional arrays are commonly used in tile-based games, where each array element represents one tile. They’re also used in 3d computer graphics (as matrices) in order to rotate, scale, and reflect shapes.

### 10.6 C-style Strings

Strings are the primary way in which we work with text in C++, and std::string makes working with strings in C++ easy.

Modern C++ supports two different types of strings: std::string (as part of the standard library), and C-style strings (natively, as inherited from the C language). It turns out that std::string is implemented using C-style strings.

**C-style strings**

A **C-style string** is simply an array of characters that uses a null terminator. A **null terminator** is a special character (‘\0’, ascii code 0) used to indicate the end of the string. More generically, A C-style string is called a **null-terminated string**.

To define a C-style string, simply declare a char array and initialize it with a string literal:

```C++
char myString[]{ "string" };
```

Although “string” only has 6 letters, C++ automatically adds a null terminator to the end of the string for us (we don’t need to include it ourselves). Consequently, myString is actually an array of length 7!

One important point to note is that C-style strings follow *all* the same rules as arrays. This means you can initialize the string upon creation, but you can not assign values to it using the assignment operator after that!

```C++
char myString[]{ "string" }; // ok
myString = "rope"; // not ok!
```

**C-style strings and std::cin**

There are many cases where we don’t know in advance how long our string is going to be. For example, consider the problem of writing a program where we need to ask the user to enter their name. How long is their name? We don’t know until they enter it!

```C++
#include <iostream>
#include <iterator> // for std::size
 
int main()
{
    char name[255] {}; // declare array large enough to hold 254 characters + null terminator
    std::cout << "Enter your name: ";
    std::cin.getline(name, std::size(name));
    std::cout << "You entered: " << name << '\n';
 
    return 0;
}
```

This call to cin.getline() will read up to 254 characters into name (leaving room for the null terminator!). Any excess characters will be discarded. In this way, we guarantee that we will not overflow the array!

**Manipulating C-style strings**

C++ provides many functions to manipulate C-style strings as part of the <cstring> header. Here are a few of the most useful:

strcpy() allows you to copy a string to another string. More commonly, this is used to assign a value to a string:

```C++
#include <cstring> int main(){    char source[]{ "Copy this!" };    char dest[50];    std::strcpy(dest, source);    std::cout << dest << '\n'; // prints "Copy this!"     return 0;}
```

However, strcpy() can easily cause array overflows if you’re not careful! In the following program, dest isn’t big enough to hold the entire string, so array overflow results.

```C++
#include <cstring>
 
int main()
{
    char source[]{ "Copy this!" };
    char dest[5]; // note that the length of dest is only 5 chars!
    std::strcpy(dest, source); // overflow!
    std::cout << dest << '\n';
 
    return 0;
}
```

Many programmers recommend using strncpy() instead, which allows you to specify the size of the buffer, and ensures overflow doesn’t occur. Unfortunately, strncpy() doesn’t ensure strings are null terminated, which still leaves plenty of room for array overflow.

In C++11, strcpy_s() is preferred, which adds a new parameter to define the size of the destination. However, not all compilers support this function, and to use it, you have to define __STDC_WANT_LIB_EXT1__ with integer value 1.

```C++
#define __STDC_WANT_LIB_EXT1__ 1#include <cstring> // for strcpy_sint main(){    char source[]{ "Copy this!" };    char dest[5]; // note that the length of dest is only 5 chars!    strcpy_s(dest, 5, source); // A runtime error will occur in debug mode    std::cout << dest << '\n';     return 0;}
```

Because not all compilers support strcpy_s(), strlcpy() is a popular alternative -- even though it’s non-standard, and thus not included in a lot of compilers. It also has its own set of issues. In short, there’s no universally recommended solution here if you need to copy a C-style string.

Another useful function is the strlen() function, which returns the length of the C-style string (without the null terminator).

Note the difference between strlen() and std::size(). strlen() prints the number of characters before the null terminator, whereas std::size (or the sizeof() trick) returns the size of the entire array, regardless of what’s in it.

Other useful functions:

- strcat() -- Appends one string to another (dangerous)
- strncat() -- Appends one string to another (with buffer length check)
- strcmp() -- Compare two strings (returns 0 if equal)
- strncmp() -- Compare two strings up to a specific number of characters (returns 0 if equal)

Here’s an example program using some of the concepts in this lesson:

```C++
#include <cstring>
#include <iostream>
#include <iterator> // for std::size
 
int main()
{
    // Ask the user to enter a string
    char buffer[255] {};
    std::cout << "Enter a string: ";
    std::cin.getline(buffer, std::size(buffer));
 
    int spacesFound{ 0 };
    int bufferLength{ static_cast<int>(std::strlen(buffer)) };
    // Loop through all of the characters the user entered
    for (int index{ 0 }; index < bufferLength; ++index)
    {
        // If the current character is a space, count it
        if (buffer[index] == ' ')
            ++spacesFound;
    }
 
    std::cout << "You typed " << spacesFound << " spaces!\n";
 
    return 0;
}
```

Note that we put `strlen(buffer)` outside the loop so that the string length is only calculated once, not every time the loop condition is checked.

**Don’t use C-style strings**

It is important to know about C-style strings because they are used in a lot of code. However, now that we’ve explained how they work, we’re going to recommend that you avoid them altogether whenever possible! Unless you have a specific, compelling reason to use C-style strings, use `std::string` (defined in the `<string>` header) instead. `std::string` is easier, safer, and more flexible. In the rare case that you do need to work with fixed buffer sizes and C-style strings (e.g. for memory-limited devices), we’d recommend using a well-tested 3rd party string library designed for the purpose, or `std::string_view`, which is covered in the next lesson, instead.

**Rule**: Use `std::string` or `std::string_view` (next lesson) instead of C-style strings.

### 10.7 An Introduction to `std::string_view`

In the previous lesson, we talked about C-style strings, and the dangers of using them. C-style strings are fast, but they’re not as easy to use and as safe as `std::string`.

But `std::string` has some of its own downsides, particularly when it comes to const strings.

Consider the following example:

```C++
#include <iostream>#include <string> int main(){  char text[]{ "hello" };  std::string str{ text };  std::string more{ str };   std::cout << text << ' ' << str << ' ' << more << '\n';   return 0;}
```

Internally, `main` copies the string “hello” 3 times, resulting in 4 copies. First, there is the string literal “hello”, which is known at compile-time and stored in the binary. One copy is created when we create the `char[]`. The following two `std::string` objects create one copy of the string each. Because `std::string` is designed to be modifiable, each `std::string` must contain its own copy of the string, so that a given `std::string` can be modified without affecting any other `std::string` object.

This holds true for const `std::string`, even though they can’t be modified.

Consider a window in your house, looking at a car sitting on the street. You can look through the window and see the car, but you can’t touch or move the car. Your window just provides a view to the car, which is a completely separate object.

C++17 introduces another way of using strings, `std::string_view`, which lives in the <string_view> header.

Unlike `std::string`, which keeps its own copy of the string, `std::string_view` provides a *view* of a string that is defined elsewhere.

We can re-write the above code to use `std::string_view` by replacing every `std::string` with `std::string_view`.

```C++
#include <iostream>
#include <string_view>
 
int main()
{
  std::string_view text{ "hello" }; // view the text "hello", which is stored in the binary
  std::string_view str{ text }; // view of the same "hello"
  std::string_view more{ str }; // view of the same "hello"
 
  std::cout << text << ' ' << str << ' ' << more << '\n';
 
  return 0;
}
```

The output is the same, but no more copies of the string “hello” are created. The string “hello” is stored in the binary and is not allocated at run-time. `text` is only a view onto the string “hello”, so no copy has to be created. When we copy a `std::string_view`, the new `std::string_view` observes the same string as the copied-from `std::string_view` is observing. This means that neither `str` nor `more` create any copies. They are views onto the existing string “hello”.

`std::string_view` is not only fast, but has many of the functions that we know from `std::string`.

```C++
#include <iostream>#include <string_view> int main(){  std::string_view str{ "Trains are fast!" };   std::cout << str.length() << '\n'; // 16  std::cout << str.substr(0, str.find(' ')) << '\n'; // Trains  std::cout << (str == "Trains are fast!") << '\n'; // 1   // Since C++20  std::cout << str.starts_with("Boats") << '\n'; // 0  std::cout << str.ends_with("fast!") << '\n'; // 1   std::cout << str << '\n'; // Trains are fast!   return 0;}
```

Because `std::string_view` doesn’t create a copy of the string, if we change the viewed string, the changes are reflected in the `std::string_view`.

```C++
#include <iostream>
#include <string_view>
 
int main()
{
  char arr[]{ "Gold" };
  std::string_view str{ arr };
 
  std::cout << str << '\n'; // Gold
 
  // Change 'd' to 'f' in arr
  arr[3] = 'f';
 
  std::cout << str << '\n'; // Golf
 
  return 0;
}
```

We modified `arr`, but `str` appears to be changing as well. That’s because `arr` and `str` share their string. When you use a `std::string_view`, it’s best to avoid modifications to the underlying string for the remainder of the `std::string_view`‘s life to prevent confusion and errors.

**Best practice**: Use `std::string_view` instead of C-style strings.

Prefer `std::string_view` over `std::string` for read-only strings, unless you already have a `std::string`.

**View modification functions**

Back to our window analogy, consider a window with curtains. We can close either the left or right curtain to reduce what we can see. We don’t change what’s outside, we just reduce the visible area.

Similarly, `std::string_view` contains functions that let us manipulate the *view* of the string. This allows us to change the view without modifying the viewed string.

The functions for this are `remove_prefix`, which removes characters from the left side of the view, and `remove_suffix`, which removes characters from the right side of the view.

```C++
#include <iostream>
#include <string_view>
 
int main()
{
  std::string_view str{ "Peach" };
 
  std::cout << str << '\n';
 
  // Ignore the first character.
  str.remove_prefix(1);
 
  std::cout << str << '\n';
 
  // Ignore the last 2 characters.
  str.remove_suffix(2);
 
  std::cout << str << '\n';
 
  return 0;
}
```

Unlike real curtains, a `std::string_view` cannot be opened back up. Once you change the visible area, you can’t go back (There are tricks which we won’t go into).

**std::string_view works with non-null-terminated strings**

Unlike C-style strings and `std::string`, `std::string_view` doesn’t use null terminators to mark the end of the string. Rather, it knows where the string ends because it keeps track of its length.

```C++
#include <iostream>
#include <iterator> // For std::size
#include <string_view>
 
int main()
{
  // No null-terminator.
  char vowels[]{ 'a', 'e', 'i', 'o', 'u' };
 
  // vowels isn't null-terminated. We need to pass the length manually.
  // Because vowels is an array, we can use std::size to get its length.
  std::string_view str{ vowels, std::size(vowels) };
 
  std::cout << str << '\n'; // This is safe. std::cout knows how to print std::string_views.
 
  return 0;
}
```

**Ownership issues**

Being only a view, a `std::string_view`‘s lifetime is independent of that of the string it is viewing. If the viewed string goes out of scope, `std::string_view` has nothing to observe and accessing it causes undefined behavior. The string that a `std::string_view` is viewing has to have been created somewhere else. It might be a string literal that lives as long as the program does or it was created by a `std::string`, in which case the string lives until the `std::string` decides to destroy it or the `std::string` dies. `std::string_view` can’t create any strings on its own, because it’s just a view.

The same can happen when we create a `std::string_view` from a `std::string` and then modify the `std::string`. Modifying a `std::string` can cause its internal string to die and be replaced with a new one in a different place. The `std::string_view` will still look at where the old string was, but it’s not there anymore.

**Warning**: Make sure that the underlying string viewed with a `std::string_view` does not go out of scope and isn’t modified while using the std::string_view.

**Converting a** `std::string_view` **to a** `std::string`

An std::string_view will not implicitly convert to a `std::string`, but can be explicitly converted:

```C++
#include <iostream>
#include <string>
#include <string_view>
 
void print(std::string s)
{
  std::cout << s << '\n';
}
 
int main()
{
  std::string_view sv{ "balloon" };
 
  sv.remove_suffix(3);
 
  // print(sv); // compile error: won't implicitly convert
 
  std::string str{ sv }; // okay
 
  print(str); // okay
 
  print(static_cast<std::string>(sv)); // okay
	   
  return 0;
}
```

**Converting a `std::string_view` to a C-style string**

Some old functions (such as the old strlen function) still expect C-style strings. To convert a `std::string_view` to a C-style string, we can do so by first converting to a `std::string`:

```C++
#include <cstring>
#include <iostream>
#include <string>
#include <string_view>
 
int main()
{
  std::string_view sv{ "balloon" };
 
  sv.remove_suffix(3);
 
  // Create a std::string from the std::string_view
  std::string str{ sv };
 
  // Get the null-terminated C-style string.
  const char* szNullTerminated{ str.c_str() };
 
  // Pass the null-terminated string to the function that we want to use.
  std::cout << str << " has " << std::strlen(szNullTerminated) << " letter(s)\n";
 
  return 0;
}
```

However, creating a `std::string` every time we want to pass a `std::string_view` as a C-style string is expensive, so this should be avoided if possible.







### 10.8 Introduction to Pointers

When our program instantiates a variable, a free memory address is automatically assigned to the variable, and any value we assign to the variable is stored in this memory address.

**The address-of operator (&)**

The address-of operator (&) allows us to see what memory address is assigned to a variable. 

**The indirection operator (*)**

Getting the address of a variable isn’t very useful by itself.

The indirection operator (*) (also called dereference operator) allows us to access the value at a particular address:

```C++
#include <iostream> int main(){    int x{ 5 };    std::cout << x << '\n'; // print the value of variable x    std::cout << &x << '\n'; // print the memory address of variable x    std::cout << *(&x) << '\n'; /// print the value at the memory address of variable x (parenthesis not required, but make it easier to read)     return 0;}
```

**Pointers**

With the address-of operator and indirection operators now added to our toolkits, we can now talk about pointers. A **pointer** is a variable that holds a *memory address* as its value.

Pointers are typically seen as one of the most confusing parts of the C++ language, but they’re surprisingly simple when explained properly.

**Declaring a pointer**

Pointer variables are declared just like normal variables, only with an asterisk between the data type and the variable name. Note that this asterisk is *not* an indirection. It is part of the pointer declaration syntax.

```C++
int* iPtr{}; // a pointer to an integer value
double* dPtr{}; // a pointer to a double value
 
int *iPtr2{}; // also valid syntax
int * iPtr3{}; // also valid syntax (but don't do this, it looks like multiplication)
 
// When declaring multiple variables in one line, the asterisk has to appear
// once for every variable.
int *iPtr4{}, *iPtr5{}; // declare two pointers to integer variables (not recommended)
```

Syntactically, C++ will accept the asterisk next to the data type, next to the variable name, or even in the middle.

**Best practice**: When declaring a pointer variable, put the asterisk next to the type to make it easier to distinguish it from an indirection.

Just like normal variables, pointers are not initialized when declared. If not initialized with a value, they will contain garbage.

One note on pointer nomenclature: “X pointer” (where X is some type) is a commonly used shorthand for “pointer to an X”. So when we say, “an integer pointer”, we really mean “a pointer to an integer”.

Assigning a value to a pointer

Since pointers only hold addresses, when we assign a value to a pointer, that value has to be an address. One of the most common things to do with pointers is have them hold the address of a different variable.

To get the address of a variable, we use the address-of operator:

```C++
int v{ 5 };int* ptr{ &v }; // initialize ptr with address of variable v
```

The type of the pointer has to match the type of the variable being pointed to:

```C++
int iValue{ 5 };
double dValue{ 7.0 };
 
int* iPtr{ &iValue }; // ok
double* dPtr{ &dValue }; // ok
iPtr = &dValue; // wrong -- int pointer cannot point to the address of a double variable
dPtr = &iValue; // wrong -- double pointer cannot point to the address of an int variable
```

Note that the following is also not legal:

```C++
int* ptr{ 5 };
```

This is because pointers can only hold addresses, and the integer literal 5 does not have a memory address. If you try this, the compiler will tell you it cannot convert an integer to an integer pointer.

C++ will also not allow you to directly convert literal memory addresses to a pointer:

```C++
double* dPtr{ 0x0012FF7C }; // not okay
```

**The address-of operator returns a pointer**

It’s worth noting that the address-of operator (&) doesn’t return the address of its operand as a literal. Instead, it returns a pointer containing the address of the operand, whose type is derived from the argument (e.g. taking the address of an int will return the address in an int pointer).

We can see this in the following example:

```C++
#include <iostream>
#include <typeinfo>
 
int main()
{
	int x{ 4 };
	std::cout << typeid(&x).name() << '\n';
 
	return 0;
}
```

On Visual Studio 2013, this printed:

```C++
int *
```

(With gcc, this prints “pi” (pointer to int) instead).

**Indirection through pointers**

Once we have a pointer variable pointing at something, the other common thing to do with it is indirection through the pointer to get the value of what it’s pointing at. Indirection through a pointer evaluates to the *contents* of the address it is pointing to.

```C++
int value{ 5 };
std::cout << &value; // prints address of value
std::cout << value; // prints contents of value
 
int* ptr{ &value }; // ptr points to value
std::cout << ptr; // prints address held in ptr, which is &value
std::cout << *ptr; // Indirection through ptr (get the value that ptr is pointing to)
```

This is why pointers must have a type. Without a type, when indirecting through a pointer, the pointer wouldn’t know how to interpret the contents it was pointing to. It’s also why the type of the pointer and the variable address it’s being assigned to must match. If they did not, indirection through the pointer would misinterpret the bits as a different type.

Once assigned, a pointer value can be reassigned to another value:

```C++
int value1{ 5 };
int value2{ 7 };
 
int* ptr{};
 
ptr = &value1; // ptr points to value1
std::cout << *ptr; // prints 5
 
ptr = &value2; // ptr now points to value2
std::cout << *ptr; // prints 7
```

When the address of variable value is assigned to ptr, the following are true:

- ptr is the same as &value
- *ptr is treated the same as value

Because *ptr is treated the same as value, you can assign values to it just as if it were variable value! The following program prints `7`:

```C++
int value{ 5 };
int* ptr{ &value }; // ptr points to value
 
*ptr = 7; // *ptr is the same as value, which is assigned 7
std::cout << value << '\n'; // prints 7
```

**A warning about indirection through invalid pointers**

Pointers in C++ are inherently unsafe, and improper pointer usage is one of the best ways to crash your application.

During indirection through a pointer, the application attempts to go to the memory location that is stored in the pointer and retrieve the contents of memory. For security reasons, modern operating systems sandbox applications to prevent them from improperly interacting with other applications, and to protect the stability of the operating system itself. If an application tries to access a memory location not allocated to it by the operating system, the operating system may shut down the application.

**The size of pointers**

The size of a pointer is dependent upon the architecture the executable is compiled for -- a 32-bit executable uses 32-bit memory addresses -- consequently, a pointer on a 32-bit machine is 32 bits (4 bytes). With a 64-bit executable, a pointer would be 64 bits (8 bytes). Note that this is true regardless of the size of the object being pointed to:

**What good are pointers?**

At this point, pointers may seem a little silly, academic, or obtuse. Why use a pointer if we can just use the original variable?

It turns out that pointers are useful in many different cases:

\(1) Arrays are implemented using pointers. Pointers can be used to iterate through an array (as an alternative to array indices).
\(2) They are the only way you can dynamically allocate memory in C++. This is by far the most common use case for pointers.
\(3) They can be used to pass a function as a parameter to another function.
\(4) They can be used to achieve polymorphism when dealing with inheritance.
\(5) They can be used to have one struct/class point at another struct/class, to form a chain. This is useful in some more advanced data structures, such as linked lists and trees.

So there is actually a surprising number of uses for pointers. But don’t worry if you don’t understand what most of these are yet. Now that you understand what pointers are at a basic level, we can start taking an in-depth look at the various cases in which they’re useful, which we’ll do in subsequent lessons.

### 10.9 Null Pointers

**Null values and null pointers**

Just like normal variables, pointers are not initialized when they are instantiated. Unless a value is assigned, a pointer will point to some garbage address by default.

Besides memory addresses, there is one additional value that a pointer can hold: a null value. A **null value** is a special value that means the pointer is not pointing at anything. A pointer holding a null value is called a **null pointer**.

In C++, we can assign a pointer a null value by initializing or assigning it the literal 0:

```C++
float* ptr { 0 };  // ptr is now a null pointer
 
float* ptr2; // ptr2 is uninitialized
ptr2 = 0; // ptr2 is now a null pointer
```

Pointers convert to boolean false if they are null, and boolean true if they are non-null. Therefore, we can use a conditional to test whether a pointer is null or not:

```C++
double* ptr { 0 };
 
// pointers convert to boolean false if they are null, and boolean true if they are non-null
if (ptr)
    std::cout << "ptr is pointing to a double value.";
else
    std::cout << "ptr is a null pointer.";
```

**Best practice**: Initialize your pointers to a null value if you’re not giving them another value.

**Indirection through null pointers**

In the previous lesson, we noted that indirection through a garbage pointer would lead to undefined results. Indirection through a null pointer also results in undefined behavior. In most cases, it will crash your application.

Conceptually, this makes sense. Indirection through a pointer means “go to the address the pointer is pointing at and access the value there”. A null pointer doesn’t have an address. So when you try to access the value at that address, what should it do?

**The NULL macro**

In C++, there is a special preprocessor macro called NULL (defined in the <cstddef> header). This macro was inherited from C, where it is commonly used to indicate a null pointer.

```C++
#include <cstddef> // for NULL
 
double* ptr { NULL }; // ptr is a null pointer
```

The value of NULL is implementation defined, but is usually defined as the integer constant 0. Note: as of C++11, NULL can be defined as nullptr instead (which we’ll discuss in a bit).

**Best practice**: Because NULL is a preprocessor macro with an implementation defined value, avoid using NULL.

The perils of using 0 (or NULL) for null pointers

Note that the value of 0 isn’t a pointer type, so assigning 0 (or NULL, pre-C++11) to a pointer to denote that the pointer is a null pointer is a little inconsistent. In rare cases, when used as a literal argument, it can even cause problems because the compiler can’t tell whether we mean a null pointer or the integer 0:

```C++
#include <iostream>
#include <cstddef> // for NULL
 
void print(int x)
{
	std::cout << "print(int): " << x << '\n';
}
 
void print(int* x)
{
	if (!x)
		std::cout << "print(int*): null\n";
	else
		std::cout << "print(int*): " << *x << '\n';
}
 
int main()
{
	int* x { NULL };
	print(x); // calls print(int*) because x has type int*
	print(0); // calls print(int) because 0 is an integer literal
	print(NULL); // likely calls print(int), although we probably wanted print(int*)
 
	return 0;
}
```

In the likely case where NULL is defined as value 0, print(NULL) will call print(int), not print(int*) like you might expect for a null pointer literal.

**`nullptr` in C++11**

To address the above issues, C++11 introduces a new keyword called **nullptr**. nullptr is a keyword, much like the boolean keywords true and false are.

Starting with C++11, this should be favored instead of 0 when we want a null pointer:

```C++
int* ptr { nullptr }; // note: ptr is still an integer pointer, just set to a null value
```

C++ will implicitly convert nullptr to any pointer type. So in the above example, nullptr is implicitly converted to an integer pointer, and then the value of nullptr assigned to ptr. This has the effect of making integer pointer ptr a null pointer.

We can also call a function with a nullptr literal, which will match to any parameter that takes a pointer value:

```C++
#include <iostream>
 
void print(int x)
{
	std::cout << "print(int): " << x << '\n';
}
 
void print(int* x)
{
	if (!x)
		std::cout << "print(int*): null\n";
	else
		std::cout << "print(int*): " << *x << '\n';
}
 
int main()
{
	int* x { nullptr };
	print(x); // calls print(int*)
 
	print(nullptr); // calls print(int*) as desired
 
	return 0;
}
```

**Best practice**: Use nullptr to initialize your pointers to a null value.

**`std::nullptr_t`**

C++11 also introduces a new type called std::nullptr_t (in header <cstddef>). std::nullptr_t can only hold one value: nullptr! While this may seem kind of silly, it’s useful in one situation. If we want to write a function that accepts only a nullptr argument, what type do we make the parameter? The answer is std::nullptr_t.

```C++
#include <iostream>
#include <cstddef> // for std::nullptr_t
 
void doSomething(std::nullptr_t ptr)
{
    std::cout << "in doSomething()\n";
}
 
int main()
{
    doSomething(nullptr); // call doSomething with an argument of type std::nullptr_t
 
    return 0;
}
```

You probably won’t ever need to use this, but it’s good to know, just in case.

### 10.10 Pointers and Arrays

Pointers and arrays are intrinsically related in C++.

**Array decay**

In a previous lesson, you learned how to define a fixed array:

```C++
int array[5]{ 9, 7, 5, 3, 1 }; // declare a fixed array of 5 integers
```

To us, the above is an array of 5 integers, but to the compiler, array is a variable of type int[5]. We know what the values of array[0], array[1], array[2], array[3], and array[4] are (9, 7, 5, 3, and 1 respectively).

In all but two cases (which we’ll cover below), when a fixed array is used in an expression, the fixed array will **decay** (be implicitly converted) into a pointer that points to the first element of the array. You can see this in the following program:



```C++
#include <iostream>
 
// C++ will implicitly convert parameter array[] to *array
void printSize(int array[])
{
    // array is treated as a pointer here, not a fixed array
    std::cout << sizeof(array) << '\n'; // prints the size of a pointer, not the size of the array!
}
/*
void printSize(int* array)
{
    // array is treated as a pointer here, not a fixed array
    std::cout << sizeof(array) << '\n'; // prints the size of a pointer, not the size of the array!
}
*/
 
int main()
{
    int array[]{ 1, 1, 2, 3, 5, 8, 13, 21 };
    std::cout << sizeof(array) << '\n'; // will print sizeof(int) * array length
 
    printSize(array); // the array argument decays into a pointer here
 
    return 0;
}
```

In the above example, C++ implicitly converts parameters using the array syntax ([]) to the pointer syntax (*). That means the following two function declarations are identical:

```C++
void printSize(int array[]);
void printSize(int* array);
```

Some programmers prefer using the [] syntax because it makes it clear that the function is expecting an array, not just a pointer to a value. However, in most cases, because the pointer doesn’t know how large the array is, you’ll need to pass in the array size as a separate parameter anyway (strings being an exception because they’re null terminated).

We lightly recommend using the pointer syntax, because it makes it clear that the parameter is being treated as a pointer, not a fixed array, and that certain operations, such as sizeof(), will operate as if the parameter is a pointer.

**Best practice**: Favor the pointer syntax (*) over the array syntax ([]) for array function parameters.

**An intro to pass by address**

The fact that arrays decay into pointers when passed to a function explains the underlying reason why changing an array in a function changes the actual array argument passed in. 

**Arrays in structs and classes don’t decay**

Finally, it is worth noting that arrays that are part of structs or classes do not decay when the whole struct or class is passed to a function. This yields a useful way to prevent decay if desired, and will be valuable later when we write classes that utilize arrays.

### 10.11 Pointer Arithmetic and Array Indexing

**Pointers arithmetic**

The C++ language allows you to perform integer addition or subtraction operations on pointers. If `ptr` points to an integer, `ptr + 1` is the address of the next integer in memory after ptr. `ptr - 1` is the address of the previous integer before `ptr`.

Note that `ptr + 1` does not return the *memory address* after `ptr`, but the memory address of the *next object of the type* that `ptr` points to. If `ptr` points to an integer (assuming 4 bytes), `ptr + 3` means 3 integers (12 bytes) after `ptr`. If `ptr` points to a `char`, which is always 1 byte, `ptr + 3` means 3 chars (3 bytes) after `ptr`.

When calculating the result of a pointer arithmetic expression, the compiler always multiplies the integer operand by the size of the object being pointed to. This is called **scaling**.

**Arrays are laid out sequentially in memory**

By using the address-of operator (&), we can determine that arrays are laid out sequentially in memory. That is, elements 0, 1, 2, … are all adjacent to each other, in order.

**Pointer arithmetic, arrays, and the magic behind indexing**

In the section above, you learned that arrays are laid out in memory sequentially.

In the previous lesson, you learned that a fixed array can decay into a pointer that points to the first element (element 0) of the array.

Also in a section above, you learned that adding 1 to a pointer returns the memory address of the next object of that type in memory.

Therefore, we might conclude that adding 1 to an array should point to the second element (element 1) of the array. We can verify experimentally that this is true:

```C++
#include <iostream>
 
int main()
{
     int array[]{ 9, 7, 5, 3, 1 };
 
     std::cout << &array[1] << '\n'; // print memory address of array element 1
     std::cout << array+1 << '\n'; // print memory address of array pointer + 1 
 
     std::cout << array[1] << '\n'; // prints 7
     std::cout << *(array+1) << '\n'; // prints 7 (note the parenthesis required here)
 
    return 0;
}
```

Note that when performing indirection through the result of pointer arithmetic, parenthesis are necessary to ensure the operator precedence is correct, since operator * has higher precedence than operator +.

It turns out that when the compiler sees the subscript operator ([]), it actually translates that into a pointer addition and indirection! Generalizing, `array[n]` is the same as `*(array + n)`, where n is an integer. The subscript operator [] is there both to look nice and for ease of use (so you don’t have to remember the parenthesis).

**Using a pointer to iterate through an array**

We can use a pointer and pointer arithmetic to loop through an array. Although not commonly done this way (using subscripts is generally easier to read and less error prone), the following example goes to show it is possible:

```C++
#include <iostream>
#include <iterator> // for std::size
 
bool isVowel(char ch)
{
    switch (ch)
    {
    case 'A':
    case 'a':
    case 'E':
    case 'e':
    case 'I':
    case 'i':
    case 'O':
    case 'o':
    case 'U':
    case 'u':
        return true;
    default:
        return false;
    }
}
 
int main()
{
    char name[]{ "Mollie" };
    int arrayLength{ static_cast<int>(std::size(name)) };
    int numVowels{ 0 };
 
    for (char* ptr{ name }; ptr != (name + arrayLength); ++ptr)
    {
        if (isVowel(*ptr))
        {
            ++numVowels;
        }
    }
 
    std::cout << name << " has " << numVowels << " vowels.\n";
 
    return 0;
}
```

How does it work? This program uses a pointer to step through each of the elements in an array. Remember that arrays decay to pointers to the first element of the array. So by assigning `ptr` to name, `ptr` will also point to the first element of the array. Indirection through `ptr` is performed for each element when we call `isVowel(*ptr)`, and if the element is a vowel, `numVowels` is incremented. Then the for loop uses the ++ operator to advance the pointer to the next character in the array. The for loop terminates when all characters have been examined.

Because counting elements is common, the algorithms library offers `std::count_if`, which counts elements that fulfill a condition. We can replace the `for`-loop with a call to `std::count_if`.

```C++
#include <algorithm>
#include <iostream>
#include <iterator> // for std::begin and std::end
 
bool isVowel(char ch)
{
    switch (ch)
    {
    case 'A':
    case 'a':
    case 'E':
    case 'e':
    case 'I':
    case 'i':
    case 'O':
    case 'o':
    case 'U':
    case 'u':
        return true;
    default:
        return false;
    }
}
 
int main()
{
    char name[]{ "Mollie" };
 
    // walk through all the elements of name and count how many calls to isVowel return true
    auto numVowels{ std::count_if(std::begin(name), std::end(name), isVowel) };
 
    std::cout << name << " has " << numVowels << " vowels.\n";
 
    return 0;
}
```

`std::begin` returns an iterator (pointer) to the first element, while `std::end` returns an iterator to the element that would be one after the last. The iterator returned by `std::end` is only used as a marker, accessing it causes undefined behavior, because it doesn’t point to a real element.

`std::begin` and `std::end` only work on arrays with a known size. If the array decayed to a pointer, we can calculate begin and end manually.

```C++
// nameLength is the number of elements in the array.
std::count_if(name, name + nameLength, isVowel)
 
// Don't do this. Accessing invalid indexes causes undefined behavior.
// std::count_if(name, &name[nameLength], isVowel)
```

Note that we’re calculating `name + nameLength`, not `name + nameLength - 1`, because we don’t want the last element, but the pseudo-element one past the last.

Calculating begin and end of an array like this works for all algorithms that need a begin and end argument.

### 10.12 C-style String symbolic constants

In a previous lesson, we discussed how you could create and initialize a C-style string, like this:

```C++
#include <iostream>
 
int main()
{
    char myName[]{ "Alex" }; // fixed array
    std::cout << myName << '\n';
 
    return 0;
}
```

C++ also supports a way to create C-style string symbolic constants using pointers:

```C++
#include <iostream>
 
int main()
{
    const char* myName{ "Alex" }; // pointer to symbolic constant
    std::cout << myName << '\n';
 
    return 0;
}
```

While these above two programs operate and produce the same results, C++ deals with the memory allocation for these slightly differently.

In the fixed array case, the program allocates memory for a fixed array of length 5, and initializes that memory with the string `“Alex\0”`. Because memory has been specifically allocated for the array, you’re free to alter the contents of the array. The array itself is treated as a normal local variable, so when the array goes out of scope, the memory used by the array is freed up for other uses.

In the symbolic constant case, how the compiler handles this is implementation defined. What *usually* happens is that the compiler places the string “Alex\0” into read-only memory somewhere, and then sets the pointer to point to it. Because this memory may be read-only, best practice is to make sure the string is const.

For optimization purposes, multiple string literals may be consolidated into a single value. For example:

```C++
const char* name1{ "Alex" };
const char* name2{ "Alex" };
```

These are two different string literals with the same value. The compiler may opt to combine these into a single shared string literal, with both name1 and name2 pointed at the same address. Thus, if name1 was not const, making a change to name1 could also impact name2 (which might not be expected).

As a result of string literals being stored in a fixed location in memory, string literals have static duration rather than automatic duration (that is, they die at the end of the program, not the end of the block in which they are defined). That means that when we use string literals, we don’t have to worry about scoping issues. Thus, the following is okay:

```C++
const char* getName()
{
    return "Jack";
}
```

In the above code, `getName()` will return a pointer to C-style string “Jack”. If this function were returning any other local variable by address, the variable would be destroyed at the end of `getName()`, and we’d return a dangling pointer back to the caller. However, because string literals have static duration, “Jack” will not be destroyed when `getName()` terminates, so the caller can still successfully access it.

C-style strings are used in a lot of old or low-level code, because they have a very small memory footprint. Modern code should favor the use `std::string` and `std::string_view`, as those provide safe and easy access to the string.

**std::cout and char pointers**

At this point, you may have noticed something interesting about the way `std::cout` handles pointers of different types.

Consider the following example:

```C++
#include <iostream>
 
int main()
{
    int nArray[5]{ 9, 7, 5, 3, 1 };
    char cArray[]{ "Hello!" };
    const char* name{ "Alex" };
 
    std::cout << nArray << '\n'; // nArray will decay to type int*
    std::cout << cArray << '\n'; // cArray will decay to type char*
    std::cout << name << '\n'; // name is already type char*
 
    return 0;
}
```

On the author’s machine, this printed:

```
003AF738
Hello!
Alex
```

Why did the int array print an address, but the character arrays printed strings?

The answer is that `std::cout` makes some assumptions about your intent. If you pass it a non-char pointer, it will simply print the contents of that pointer (the address that the pointer is holding). However, if you pass it an object of type `char*` or `const char*`, it will assume you’re intending to print a string. Consequently, instead of printing the pointer’s value, it will print the string being pointed to instead!

While this is great 99% of the time, it can lead to unexpected results. Consider the following case:

```C++
#include <iostream>
 
int main()
{
    char c{ 'Q' };
    std::cout << &c;
 
    return 0;
}
```

In this case, the programmer is intending to print the address of variable c. However, &c has type char*, so std::cout tries to print this as a string! On the author’s machine, this printed:

```
Q烫烫犤哌
```

Why did it do this? Well, it assumed &c (which has type char*) was a string. So it printed the ‘Q’, and then kept going. Next in memory was a bunch of garbage. Eventually, it ran into some memory holding a 0 value, which it interpreted as a null terminator, so it stopped. What you see may be different depending on what’s in memory after variable c.

This case is somewhat unlikely to occur in real-life (as you’re not likely to actually want to print memory addresses), but it is illustrative of how things work under the hood, and how programs can inadvertently go off the rails.

### 10.13 Dynamic Memory Allocation with new and delete

**The need for dynamic memory allocation**

C++ supports three basic types of memory allocation, of which you’ve already seen two.

- **Static memory allocation** happens for static and global variables. Memory for these types of variables is allocated once when your program is run and persists throughout the life of your program.
- **Automatic memory allocation** happens for function parameters and local variables. Memory for these types of variables is allocated when the relevant block is entered, and freed when the block is exited, as many times as necessary.
- **Dynamic memory allocation** is the topic of this article.

Both static and automatic allocation have two things in common:

- The size of the variable / array must be known at compile time.
- Memory allocation and deallocation happens automatically (when the variable is instantiated / destroyed).

Most of the time, this is just fine. However, you will come across situations where one or both of these constraints cause problems, usually when dealing with external (user or file) input.

For example, we may want to use a string to hold someone’s name, but we do not know how long their name is until they enter it. Or we may want to read in a number of records from disk, but we don’t know in advance how many records there are. Or we may be creating a game, with a variable number of monsters (that changes over time as some monsters die and new ones are spawned) trying to kill the player.

If we have to declare the size of everything at compile time, the best we can do is try to make a guess the maximum size of variables we’ll need and hope that’s enough:

```C++
char name[25]; // let's hope their name is less than 25 chars!
Record record[500]; // let's hope there are less than 500 records!
Monster monster[40]; // 40 monsters maximum
Polygon rendering[30000]; // this 3d rendering better not have more than 30,000 polygons!
```

This is a poor solution for at least four reasons:

First, it leads to wasted memory if the variables aren’t actually used. For example, if we allocate 25 chars for every name, but names on average are only 12 chars long, we’re using over twice what we really need. Or consider the rendering array above: if a rendering only uses 10,000 polygons, we have 20,000 Polygons worth of memory not being used!

Second, how do we tell which bits of memory are actually used? For strings, it’s easy: a string that starts with a \0 is clearly not being used. But what about monster[24]? Is it alive or dead right now? That necessitates having some way to tell active from inactive items, which adds complexity and can use up additional memory.

Third, most normal variables (including fixed arrays) are allocated in a portion of memory called the **stack**. The amount of stack memory for a program is generally quite small -- Visual Studio defaults the stack size to 1MB. If you exceed this number, stack overflow will result, and the operating system will probably close down the program.

On Visual Studio, you can see this happen when running this program:

```C++
int main()
{
    int array[1000000]; // allocate 1 million integers (probably 4MB of memory)
}
```

Fourth, and most importantly, it can lead to artificial limitations and/or array overflows. What happens when the user tries to read in 600 records from disk, but we’ve only allocated memory for a maximum of 500 records? Either we have to give the user an error, only read the 500 records, or (in the worst case where we don’t handle this case at all) overflow the record array and watch something bad happen.

Fortunately, these problems are easily addressed via dynamic memory allocation. **Dynamic memory allocation** is a way for running programs to request memory from the operating system when needed. This memory does not come from the program’s limited stack memory -- instead, it is allocated from a much larger pool of memory managed by the operating system called the **heap**. On modern machines, the heap can be gigabytes in size.

**Dynamically allocating single variables**

To allocate a *single* variable dynamically, we use the scalar (non-array) form of the **new** operator:

```C++
new int; // dynamically allocate an integer (and discard the result)
```

In the above case, we’re requesting an integer’s worth of memory from the operating system. The new operator creates the object using that memory, and then returns a pointer containing the *address* of the memory that has been allocated.

Most often, we’ll assign the return value to our own pointer variable so we can access the allocated memory later.

```C++
int* ptr{ new int }; // dynamically allocate an integer and assign the address to ptr so we can access it later
```

We can then perform indirection through the pointer to access the memory:

```C++
*ptr = 7; // assign value of 7 to allocated memory
```

If it wasn’t before, it should now be clear at least one case in which pointers are useful. Without a pointer to hold the address of the memory that was just allocated, we’d have no way to access the memory that was just allocated for us!

**How does dynamic memory allocation work?**

Your computer has memory (probably lots of it) that is available for applications to use. When you run an application, your operating system loads the application into some of that memory. This memory used by your application is divided into different areas, each of which serves a different purpose. One area contains your code. Another area is used for normal operations (keeping track of which functions were called, creating and destroying global and local variables, etc…). We’ll talk more about those later. However, much of the memory available just sits there, waiting to be handed out to programs that request it.

When you dynamically allocate memory, you’re asking the operating system to reserve some of that memory for your program’s use. If it can fulfill this request, it will return the address of that memory to your application. From that point forward, your application can use this memory as it wishes. When your application is done with the memory, it can return the memory back to the operating system to be given to another program.

Unlike static or automatic memory, the program itself is responsible for requesting and disposing of dynamically allocated memory.

**Initializing a dynamically allocated variable**

When you dynamically allocate a variable, you can also initialize it via direct initialization or uniform initialization (in C++11):

```C++
int* ptr1{ new int (5) }; // use direct initialization
int* ptr2{ new int { 6 } }; // use uniform initialization
```

**Deleting single variables**

When we are done with a dynamically allocated variable, we need to explicitly tell C++ to free the memory for reuse. For single variables, this is done via the scalar (non-array) form of the **delete** operator:

```C++
// assume ptr has previously been allocated with operator new
delete ptr; // return the memory pointed to by ptr to the operating system
ptr = 0; // set ptr to be a null pointer (use nullptr instead of 0 in C++11)
```

**What does it mean to delete memory?**

The delete operator does not *actually* delete anything. It simply returns the memory being pointed to back to the operating system. The operating system is then free to reassign that memory to another application (or to this application again later).

Although it looks like we’re deleting a *variable*, this is not the case! The pointer variable still has the same scope as before, and can be assigned a new value just like any other variable.

Note that deleting a pointer that is not pointing to dynamically allocated memory may cause bad things to happen.

**Dangling pointers**

C++ does not make any guarantees about what will happen to the contents of deallocated memory, or to the value of the pointer being deleted. In most cases, the memory returned to the operating system will contain the same values it had before it was returned, and the pointer will be left pointing to the now deallocated memory.

A pointer that is pointing to deallocated memory is called a **dangling pointer**. Indirection through- or deleting a dangling pointer will lead to undefined behavior. Consider the following program:

```C++
#include <iostream>
 
int main()
{
    int* ptr{ new int }; // dynamically allocate an integer
    *ptr = 7; // put a value in that memory location
 
    delete ptr; // return the memory to the operating system.  ptr is now a dangling pointer.
 
    std::cout << *ptr; // Indirection through a dangling pointer will cause undefined behavior
    delete ptr; // trying to deallocate the memory again will also lead to undefined behavior.
 
    return 0;
}
```

In the above program, the value of 7 that was previously assigned to the allocated memory will probably still be there, but it’s possible that the value at that memory address could have changed. It’s also possible the memory could be allocated to another application (or for the operating system’s own usage), and trying to access that memory will cause the operating system to shut the program down.

Deallocating memory may create multiple dangling pointers. Consider the following example:

```C++
#include <iostream>
 
int main()
{
    int* ptr{ new int{} }; // dynamically allocate an integer
    int* otherPtr{ ptr }; // otherPtr is now pointed at that same memory location
 
    delete ptr; // return the memory to the operating system.  ptr and otherPtr are now dangling pointers.
    ptr = nullptr; // ptr is now a nullptr
 
    // however, otherPtr is still a dangling pointer!
 
    return 0;
}
```

There are a few best practices that can help here.

First, try to avoid having multiple pointers point at the same piece of dynamic memory. If this is not possible, be clear about which pointer “owns” the memory (and is responsible for deleting it) and which pointers are just accessing it.

Second, when you delete a pointer, if that pointer is not going out of scope immediately afterward, set the pointer to 0 (or nullptr in C++11). We’ll talk more about null pointers, and why they are useful in a bit.



### 10.15 Pointers and const

**Pointing to const variables**

So far, all of the pointers you’ve seen are non-const pointers to non-const values:

```C++
int value{ 5 };
int* ptr{ &value };
*ptr = 6; // change value to 6
```

However, what happens if value is const?

```C++
const int value{ 5 }; // value is const
int* ptr{ &value }; // compile error: cannot convert const int* to int*
*ptr = 6; // change value to 6
```

The above snippet won’t compile -- we can’t set a non-const pointer to a const variable. This makes sense: a const variable is one whose value can not be changed. Hypothetically, if we could set a non-const pointer to a const value, then we would be able perform indirection through the non-const pointer and change the value. That would violate the intention of const.

**Pointer to const value**

A **pointer to a const value** is a (non-const) pointer that points to a constant value.

To declare a pointer to a const value, use the *const* keyword before the data type:

```C++
const int value{ 5 };
const int* ptr{ &value }; // this is okay, ptr is a non-const pointer that is pointing to a "const int"
*ptr = 6; // not allowed, we can't change a const value
```

In the above example, ptr points to a const int.

So far, so good, right? Now consider the following example:

```C++
int value{ 5 }; // value is not constant
const int* ptr{ &value }; // this is still okay
```

A pointer to a constant variable can point to a non-constant variable (such as variable value in the example above). Think of it this way: a pointer to a constant variable treats the variable as constant when it is accessed through the pointer, regardless of whether the variable was initially defined as const or not.

Thus, the following is okay:

```C++
int value{ 5 };
const int* ptr{ &value }; // ptr points to a "const int"
value = 6; // the value is non-const when accessed through a non-const identifier
```

But the following is not:

```C++
int value{ 5 };
const int* ptr{ &value }; // ptr points to a "const int"
*ptr = 6; // ptr treats its value as const, so changing the value through ptr is not legal
```

Because a pointer to a const value is not const itself (it just points to a const value), the pointer can be redirected to point at other values:

```C++
int value1{ 5 };
const int *ptr{ &value1 }; // ptr points to a const int
 
int value2{ 6 };
ptr = &value2; // okay, ptr now points at some other const int
```

**Const pointers**

We can also make a pointer itself constant. A **const pointer** is a pointer whose value can not be changed after initialization.

To declare a const pointer, use the *const* keyword between the asterisk and the pointer name:

```C++
int value{ 5 };
int* const ptr{ &value };
```

Just like a normal const variable, a const pointer must be initialized to a value upon declaration. This means a const pointer will always point to the same address. In the above case, ptr will always point to the address of value (until ptr goes out of scope and is destroyed).

```C++
int value1{ 5 };
int value2{ 6 };
 
int* const ptr{ &value1 }; // okay, the const pointer is initialized to the address of value1
ptr = &value2; // not okay, once initialized, a const pointer can not be changed.
```

However, because the *value* being pointed to is still non-const, it is possible to change the value being pointed to indrectly through the const pointer:

```C++
int value{ 5 };
int* const ptr{ &value }; // ptr will always point to value
*ptr = 6; // allowed, since ptr points to a non-const int
```

**Const pointer to a const value**

Finally, it is possible to declare a const pointer to a const value by using the *const* keyword both before the type and before the variable name:

```C++
int value{ 5 };
const int* const ptr{ &value };
```

A const pointer to a const value can not be set to point to another address, nor can the value it is pointing to be changed through the pointer.

**Recapping**

To summarize, you only need to remember 4 rules, and they are pretty logical:

- A non-const pointer can be redirected to point to other addresses.
- A const pointer always points to the same address, and this address can not be changed.

- A pointer to a non-const value can change the value it is pointing to. These can not point to a const value.
- A pointer to a const value treats the value as const (even if it is not), and thus can not change the value it is pointing to.

Keeping the declaration syntax straight can be challenging. Just remember that the type of value the pointer points to is always on the far left:

```C++
int value{ 5 };
const int* ptr1{ &value }; // ptr1 points to a "const int", so this is a pointer to a const value.
int* const ptr2{ &value }; // ptr2 points to an "int", so this is a const pointer to a non-const value.
const int* const ptr3{ &value }; // ptr3 points to a "const int", so this is a const pointer to a const value.
```

**Conclusion**

Pointers to const values are primarily used in function parameters (for example, when passing an array to a function) to help ensure the function doesn’t inadvertently change the passed in argument. We will discuss this further in the section on functions.

### 10.16 Reference Variables

So far, we’ve discussed two different kinds of variables:

- Normal variables, which hold values directly.
- Pointers, which hold the address of another value (or null) and their value can be retrieved through indirection of address they point to.

References are the third basic kind of variable that C++ supports. A **reference** is a C++ variable that acts as an alias to another object or value.

C++ supports three kinds of references:

1. References to non-const values (typically just called “references”, or “non-const references”), which we’ll discuss in this lesson.
2. References to const values (often called “const references”), which we’ll discuss in the next lesson.
3. C++11 added r-value references, which we cover in detail in the chapter on move semantics.

**References to non-const values**

A reference (to a non-const value) is declared by using an ampersand (&) between the reference type and the variable name:

```C++
int value{ 5 }; // normal integer
int &ref{ value }; // reference to variable value
```

In this context, the ampersand does not mean “address of”, it means “reference to”.

References to non-const values are often just called “references” for short.

Just like the position of the asterisk of pointers, it doesn’t matter if you place the ampersand at the type or at the variable name.

```C++
int value{ 5 };
// These two do the same.
int& ref1{ value };
int &ref2{ value };
```

**Best practice**: When declaring a reference variable, put the ampersand next to the type to make it easier to distinguish it from the address-of operator.

**References as aliases**

References generally act identically to the values they’re referencing. In this sense, a reference acts as an alias for the object being referenced. For example:

```C++
int x{ 5 }; // normal integer
int& y{ x }; // y is a reference to x
int& z{ y }; // z is also a reference to x
```

In the above snippet, setting or getting the value of x, y, or z will all do the same thing (set or get the value of x).

Let’s take a look at references in use:

```C++
#include <iostream>
 
int main()
{
    int value{ 5 }; // normal integer
    int& ref{ value }; // reference to variable value
 
    value = 6; // value is now 6
    ref = 7; // value is now 7
 
    std::cout << value << '\n'; // prints 7
    ++ref;
    std::cout << value << '\n'; // prints 8
 
    return 0;
}
```

In the above example, ref and value are treated synonymously.

Using the address-of operator on a reference returns the address of the value being referenced:

```C++
std::cout << &value << '\n'; // prints 0012FF7C
std::cout << &ref << '\n'; // prints 0012FF7C
```

**l-values and r-values**

In C++, variables are a type of l-value (pronounced ell-value). An **l-value** is a value that has an address (in memory). Since all variables have addresses, all variables are l-values. The name l-value came about because l-values are the only values that can be on the left side of an assignment statement. When we do an assignment, the left hand side of the assignment operator must be an l-value. Consequently, a statement like `5 = 6;` will cause a compile error, because 5 is not an l-value. The value of 5 has no memory, and thus nothing can be assigned to it. 5 means 5, and its value can not be reassigned. When an l-value has a value assigned to it, the current value at that memory address is overwritten.

The opposite of l-values are r-values (pronounced arr-values). An **r-value** is an expression that is not an l-value. Examples of r-values are literals (such as 5, which evaluates to 5) and non-l-value expressions (such as 2 + x).

The key takeaway is that on the left side of the assignment, you must have something that represents a memory address (such as a variable). Everything on the right side of the assignment will be evaluated to produce a value.

**Note**: const variables are considered non-modifiable l-values.

**References must be initialized**

References must be initialized when created:

```C++
int value{ 5 };
int& ref{ value }; // valid reference, initialized to variable value
 
int& invalidRef; // invalid, needs to reference something
```

Unlike pointers, which can hold a null value, there is no such thing as a null reference.

References to non-const values can only be initialized with non-const l-values. They can not be initialized with const l-values or r-values.

```C++
int x{ 5 };
int& ref1{ x }; // okay, x is an non-const l-value
 
const int y{ 7 };
int& ref2{ y }; // not okay, y is a const l-value
 
int& ref3{ 6 }; // not okay, 6 is an r-value
```

Note that in the middle case, you can’t initialize a non-const reference with a const object -- otherwise you’d be able to change the value of the const object through the reference, which would violate the const-ness of the object.

**References can not be reassigned**

Once initialized, a reference can not be changed to reference another variable. Consider the following snippet:

```C++
int value1{ 5 };
int value2{ 6 };
 
int& ref{ value1 }; // okay, ref is now an alias for value1
ref = value2; // assigns 6 (the value of value2) to value1 -- does NOT change the reference!
```

Note that the second statement may not do what you might expect! Instead of changing ref to reference variable value2, it assigns the *value* of value2 to value1.

**References as function parameters**

References are most often used as function parameters. In this context, the reference parameter acts as an alias for the argument, and no copy of the argument is made into the parameter. This can lead to better performance if the argument is large or expensive to copy.

In a previous lesson we talked about how passing a pointer argument to a function allows the function to perform indirection through the pointer to modify the argument’s value directly.

References work similarly in this regard. Because the reference parameter acts as an alias for the argument, a function that uses a reference parameter is able to modify the argument passed in:

```C++
#include <iostream>
 
// ref is a reference to the argument passed in, not a copy
void changeN(int& ref)
{
	ref = 6;
}
 
int main()
{
	int n{ 5 };
 
	std::cout << n << '\n';
 
	changeN(n); // note that this argument does not need to be a reference
 
	std::cout << n << '\n';
	return 0;
}
```

When argument n is passed to the function, the function parameter ref is set as a reference to argument n. This allows the function to change the value of n through ref! Note that n does not need to be a reference itself.

**Best practice**: Pass arguments by non-const reference (rather than by pointer) when the argument needs to be modified by the function.

The primary downside of using non-const references as function parameters is that the argument must be a non-const l-value. This can be restrictive. We’ll talk more about this (and how to get around it) in the next lesson.

**Using references to pass C-style arrays to functions**

One of the most annoying issues with C-style arrays is that in most cases they decay to pointers when evaluated. However, if a C-style array is passed by reference, this decaying does not happen.

Here’s an example (h/t to reader nascardriver):

```C++
#include <iostream>
#include <iterator> 
 
// Note: You need to specify the array size in the function declaration
void printElements(int (&arr)[4])
{
  int length{ static_cast<int>(std::size(arr)) }; // we can now do this since the array won't decay
  
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

Note that in order for this to work, you explicitly need to define the array size in the parameter.

**References as shortcuts**

A secondary (much less used) use of references is to provide easier access to nested data. Consider the following struct:

```C++
struct Something
{
    int value1;
    float value2;
};
 
struct Other
{
    Something something;
    int otherValue;
};
 
Other other;
```

Let’s say we needed to work with the value1 field of the Something struct of other. Normally, we’d access that member as `other.something.value1`. If there are many separate accesses to this member, the code can become messy. References allow you to more easily access the member:

```C++
int& ref{ other.something.value1 };
// ref can now be used in place of other.something.value1
```

The following two statements are thus identical:

```C++
other.something.value1 = 5;
ref = 5;
```

**References vs pointers**

References and pointers have an interesting relationship -- a reference acts like a pointer that implicitly performs indirection through it when accessed (references are usually implemented internally by the compiler using pointers). 

Because references must be initialized to valid objects (cannot be null) and can not be changed once set, references are generally much safer to use than pointers (since there’s no risk of indirection through a null pointer). However, they are also a bit more limited in functionality accordingly.

If a given task can be solved with either a reference or a pointer, the reference should generally be preferred. Pointers should only be used in situations where references are not sufficient (such as dynamically allocating memory).

**Summary**

References allow us to define aliases to other objects or values. References to non-const values can only be initialized with non-const l-values. References can not be reassigned once initialized.

References are most often used as function parameters when we either want to modify the value of the argument, or when we want to avoid making an expensive copy of the argument.

### 10.17 References and Const

**Reference to const value**

Just like it’s possible to declare a pointer to a const value, it’s also possible to declare a reference to a const value. This is done by declaring a reference using the const keyword.

```C++
const int apples{ 5 };
const int &ref{ apples }; // ref is a reference to a const value
```

A reference to a const value is often called a **const reference** for short, though this does make for some inconsistent nomenclature with pointers.

**Initializing references to const values**

Unlike references to non-const values, which can only be initialized with non-const l-values, references to const values can be initialized with non-const l-values, const l-values, and r-values.

```C++
int x{ 5 };
const int& ref1{ x }; // okay, x is a non-const l-value
 
const int y{ 7 };
const int& ref2{ y }; // okay, y is a const l-value
 
const int& ref3{ 6 }; // okay, 6 is an r-value
```

Much like a pointer to a const value, a reference to a const value can reference a non-const variable. When accessed through a reference to a const value, the value is considered const even if the original variable is not:

```C++
int apples{ 5 };
const int& ref{ apples }; // create const reference to variable apples
 
apples = 6; // okay, apples is non-const
ref = 7; // illegal -- ref is const
```

**References to r-values extend the lifetime of the referenced value**

Normally r-values have expression scope, meaning the values are destroyed at the end of the expression in which they are created.

```C++
std::cout << 2 + 3 << '\n'; // 2 + 3 evaluates to r-value 5, which is destroyed at the end of this statement
```

However, when a reference to a const value is initialized with an r-value, the lifetime of the r-value is extended to match the lifetime of the reference.

```C++
int somefcn()
{
    const int& ref{ 2 + 3 }; // normally the result of 2+3 has expression scope and is destroyed at the end of this statement
    // but because the result is now bound to a reference to a const value...
    std::cout << ref << '\n'; // we can use it here
} // and the lifetime of the r-value is extended to here, when the const reference dies
```

**Const references as function parameters**

References used as function parameters can also be const. This allows us to access the argument without making a copy of it, while guaranteeing that the function will not change the value being referenced.

```C++
// ref is a const reference to the argument passed in, not a copy
void changeN(const int& ref)
{
	ref = 6; // not allowed, ref is const
}
```

References to const values are particularly useful as function parameters because of their versatility. A const reference parameter allows you to pass in a non-const l-value argument, a const l-value argument, a literal, or the result of an expression:

```C++
#include <iostream>
 
void printIt(const int& x)
{
    std::cout << x;
}
 
int main()
{
    int a{ 1 };
    printIt(a); // non-const l-value
 
    const int b{ 2 };
    printIt(b); // const l-value
 
    printIt(3); // literal r-value
 
    printIt(2+b); // expression r-value
 
    return 0;
}
```

To avoid making unnecessary, potentially expensive copies, variables that are not pointers or fundamental data types (int, double, etc…) should be generally passed by (const) reference. Fundamental data types should be passed by value, unless the function needs to change them. There are a few exceptions to this rule, namely types that are so small that it’s faster for the CPU to copy them than having to perform an extra indirection for a reference.

**A reminder**: References act like pointers. The compiler adds the indirection, which we’d do manually on a pointer using an asterisk, for us.

One of those fast types is `std::string_view`. You’ll learn about more exceptions later. If you’re uncertain if a non-fundamental type is fast to pass by value, pass it by const reference.

**Best practice**: Pass non-pointer, non-fundamental data type variables (such as structs) by (const) reference, unless you know that passing it by value is faster.

### 10.18 Member Selection with Pointers and References

It is common to have either a pointer or a reference to a struct (or class). As you learned previously, you can select the member of a struct using the **member selection operator (.)**:

```C++
struct Person
{
    int age{};
    double weight{};
};
 
Person person{};
 
// Member selection using actual struct variable
person.age = 5;

// Member selection using reference to struct
Person& ref{ person };
ref.age = 5;

//However, with a pointer, you need to use the arrow operator:
// Member selection using pointer to struct
Person* ptr{ &person };
ptr->age = 5;
```

The arrow operator does the same as an indirection followed by the . member selection operator

```C++
(*ptr).age = 5;
// same as
ptr->age = 5;
```

Note that indirection through the pointer must be enclosed in parentheses, because the member selection operator has a higher precedence than the indirection operator.

The arrow operator is not only easier to type, but is also much less prone to error because the indirection is implicitly done for you, so there are no precedence issues to worry about. Consequently, when doing member access through a pointer, always use the `->` operator instead of the `.` operator.

**Best practice**: When using a pointer to access the value of a member, use operator-> instead of operator. (the . operator)

The member selection operator is always applied to the currently selected variable. If you have a mix of pointer- and normal member variables, you can see member selections where . and -> are mixed.

```C++
#include <iostream>
#include <string>
 
struct Paw
{
    int claws{};
};
 
struct Animal
{
    std::string name{};
    Paw paw{};
};
 
int main()
{
    Animal puma{ "Puma", { 5 } };
 
    Animal* pointy{ &puma };
 
    // pointy is a pointer, use ->
    // paw is not a pointer, use .
    std::cout << pointy->paw.claws << '\n';
 
    return 0;
}
```

### 10.19 For-each loop

While *for loops* provide a convenient and flexible way to iterate through an array, they are also easy to mess up and prone to off-by-one errors.

There’s a simpler and safer type of loop called a **for-each** loop (also called a **range-based for-loop**) for cases where we want to iterate through every element in an array (or other list-type structure).

**For-each loops**

The *for-each* statement has a syntax that looks like this:

```tex
for (element_declaration : array)
   statement;
```

When this statement is encountered, the loop will iterate through each element in array, assigning the value of the current array element to the variable declared in element_declaration. For best results, element_declaration should have the same type as the array elements, otherwise type conversion will occur.

Let’s take a look at a simple example that uses a *for-each* loop to print all of the elements in an array named fibonacci:

```C++
#include <iostream>
 
int main()
{
    constexpr int fibonacci[]{ 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89 };
    for (int number : fibonacci) // iterate over array fibonacci
    {
       std::cout << number << ' '; // we access the array element for this iteration through variable number
    }
 
    std::cout << '\n';
 
    return 0;
}
```

Let’s take a closer look at how this works. First, the *for loop* executes, and variable number is set to the value of the first element, which has value 0. The program executes the statement, which prints 0. Then the *for loop* executes again, and number is set to the value of the second element, which has value 1. The statement executes again, which prints 1. The *for loop* continues to iterate through each of the numbers in turn, executing the statement for each one, until there are no elements left in the array to iterate over. At that point, the loop terminates, and the program continues execution (returning 0 to the operating system).

**For each loops and the auto keyword**

Because element_declaration should have the same type as the array elements, this is an ideal case in which to use the `auto` keyword, and let C++ deduce the type of the array elements for us.

Here’s the above example, using auto:

```C++
#include <iostream>
 
int main()
{
    constexpr int fibonacci[]{ 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89 };
    for (auto number : fibonacci) // type is auto, so number has its type deduced from the fibonacci array
    {
       std::cout << number << ' ';
    }
 
    std::cout << '\n';
 
    return 0;
}
```

**For-each loops and references**

In the following for-each example, our element declarations are declared by value:

```C++
std::string array[]{ "peter", "likes", "frozen", "yogurt" };
    for (auto element : array) // element will be a copy of the current array element
    {
        std::cout << element << ' ';
    }
```

This means each array element iterated over will be copied into variable element. Copying array elements can be expensive, and most of the time we really just want to refer to the original element. Fortunately, we can use references for this:

```C++
std::string array[]{ "peter", "likes", "frozen", "yogurt" };
    for (auto& element: array) // The ampersand makes element a reference to the actual array element, preventing a copy from being made
    {
        std::cout << element << ' ';
    }
```

In the above example, element will be a reference to the currently iterated array element, avoiding having to make a copy. Also any changes to element will affect the array being iterated over, something not possible if element is a normal variable.

And, of course, it’s a good idea to make your reference `const` if you’re intending to use it in a read-only fashion:

```C++
std::string array[]{ "peter", "likes", "frozen", "yogurt" };
    for (const auto& element: array) // element is a const reference to the currently iterated array element
    {
        std::cout << element << ' ';
    }
```

**Best practice**: In for-each loops element declarations, if your elements are non-fundamental types, use references or `const` references for performance reasons.

**Note**: An array that decayed to a pointer cannot be used in a for-each loop.

**For-each loops and non-arrays**

*For-each* loops don’t only work with fixed arrays, they work with many kinds of list-like structures, such as vectors (e.g. `std::vector`), linked lists, trees, and maps. We haven’t covered any of these yet, so don’t worry if you don’t know what these are. Just remember that for each loops provide a flexible and generic way to iterate through more than just arrays.

```C++
#include <iostream>
#include <vector>
 
int main()
{
    std::vector fibonacci{ 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89 }; // note use of std::vector here rather than a fixed array
    // Before C++17
    // std::vector<int> fibonacci{ 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89 };
 
    for (auto number : fibonacci)
    {
        std::cout << number << ' ';
    }
 
    std::cout << '\n';
 
    return 0;
}
```

**For-each doesn’t work with pointers to an array**

In order to iterate through the array, for-each needs to know how big the array is, which means knowing the array size. Because arrays that have decayed into a pointer do not know their size, for-each loops will not work with them!

```C++
#include <iostream>
 
int sumArray(const int array[]) // array is a pointer
{
    int sum{ 0 };
 
    for (auto number : array) // compile error, the size of array isn't known
    {
        sum += number;
    }
 
    return sum;   
}
 
int main()
{
     constexpr int array[]{ 9, 7, 5, 3, 1 };
 
     std::cout << sumArray(array) << '\n'; // array decays into a pointer here
 
     return 0;
}
```

Similarly, dynamic arrays won’t work with for-each loops for the same reason.

**Can I get the index of the current element?**

*For-each* loops do *not* provide a direct way to get the array index of the current element. This is because many of the structures that *for-each* loops can be used with (such as linked lists) are not directly indexable!

Since C++20, range-based for-loops can be used with an **init-statement** just like the init-statement in if-statements. We can use the init-statement to create a manual index counter without polluting the function in which the for-loop is placed.

The init-statement is placed right before the loop variable:

```
for (init-statement; element_declaration : array)
   statement;
```

In the following code, we have two arrays which are correlated by index. For example, the student with the name at `names[3]` has a score of `scores[3]`. Whenever a student with a new high score is found, we print their name and difference in points to the previous high score.

```C++
#include <iostream>
 
int main()
{
    std::string names[]{ "Alex", "Betty", "Caroline", "Dave", "Emily" }; // Names of the students
    constexpr int scores[]{ 84, 92, 76, 81, 56 };
    int maxScore{ 0 };
 
    for (int i{ 0 }; auto score : scores) // i is the index of the current element
    {
        if (score > maxScore)
        {
            std::cout << names[i] << " beat the previous best score of " << maxScore << " by " << (score - maxScore) << " points!\n";
            maxScore = score;
        }
        
        ++i;
    }
 
    std::cout << "The best score was " << maxScore << '\n';
 
    return 0;
}
```

The `int i{ 0 };` is the init-statement, it only gets executed once when the loop starts. At the end of each iteration, we increment `i`, similar to a normal for-loop. However, if we were to use `continue` inside the loop, the `++i` would get skipped, leading to unexpected results. If you use `continue`, you need to make sure that `i` gets incremented before the `continue` is encountered.

Before `C++20`, the index variable `i` had to be declared outside of the loop, which could lead to name conflicts when we wanted to define another variable named `i` later in the function.

### 10.20 void pointers

The **void pointer**, also known as the generic pointer, is a special type of pointer that can be pointed at objects of any data type! A void pointer is declared like a normal pointer, using the void keyword as the pointer’s type:

```C++
void* ptr; // ptr is a void pointer
```

A void pointer can point to objects of any data type:

```C++
int nValue;
float fValue;
 
struct Something
{
    int n;
    float f;
};
 
Something sValue;
 
void* ptr;
ptr = &nValue; // valid
ptr = &fValue; // valid
ptr = &sValue; // valid
```

However, because the void pointer does not know what type of object it is pointing to, dereferencing a void pointer is illegal. Instead, the void pointer must first be cast to another pointer type before the dereference can be performed.

```C++
int value{ 5 };
void* voidPtr{ &value };
 
// std::cout << *voidPtr << '\n'; // illegal: dereference of void pointer
 
int* intPtr{ static_cast<int*>(voidPtr) }; // however, if we cast our void pointer to an int pointer...
 
std::cout << *intPtr << '\n'; // then we can dereference the result
```

The next obvious question is: If a void pointer doesn’t know what it’s pointing to, how do we know what to cast it to? Ultimately, that is up to you to keep track of.





**Void pointer miscellany**

Void pointers can be set to a null value:

```C++
void* ptr{ nullptr }; // ptr is a void pointer that is currently a null pointer
```

Although some compilers allow deleting a void pointer that points to dynamically allocated memory, doing so should be avoided, as it can result in undefined behavior.

It is not possible to do pointer arithmetic on a void pointer. This is because pointer arithmetic requires the pointer to know what size object it is pointing to, so it can increment or decrement the pointer appropriately.

Note that there is no such thing as a void reference. This is because a void reference would be of type void &, and would not know what type of value it referenced.

**Conclusion**

In general, it is a good idea to avoid using void pointers unless absolutely necessary, as they effectively allow you to avoid type checking. This allows you to inadvertently do things that make no sense, and the compiler won’t complain about it. For example, the following would be valid:

```C++
int nValue{ 5 };
printValue(&nValue, Type::tCString);
```

But who knows what the result would actually be!

Although the above function seems like a neat way to make a single function handle multiple data types, C++ actually offers a much better way to do the same thing (via function overloading) that retains type checking to help prevent misuse. Many other places where void pointers would once be used to handle multiple data types are now better done using templates, which also offer strong type checking.

However, very occasionally, you may still find a reasonable use for the void pointer. Just make sure there isn’t a better (safer) way to do the same thing using other language mechanisms first!

### 10.21 Pointers to Pointer and Dynamic Multidimensional Arrays

A pointer to a pointer is exactly what you’d expect: a pointer that holds the address of another pointer.

**Pointers to pointers**

A normal pointer to an int is declared using a single asterisk:

```C++
int* ptr; // pointer to an int, one asterisk
```

A pointer to a pointer to an int is declared using two asterisks

```C++
int** ptrptr; // pointer to a pointer to an int, two asterisks
```

A pointer to a pointer works just like a normal pointer — you can perform indirection through it to retrieve the value pointed to. And because that value is itself a pointer, you can perform indirection through it again to get to the underlying value. These indirections can be done consecutively:

```C++
int value { 5 };
 
int* ptr { &value };
std::cout << *ptr; // Indirection through pointer to int to get int value
 
int** ptrptr { &ptr };
std::cout << **ptrptr; // first indirection to get pointer to int, second indirection to get int value
```

Note that you can not set a pointer to a pointer directly to a value:

```C++
int value { 5 };
int** ptrptr { &&value }; // not valid
```

This is because the address of operator (operator&) requires an lvalue, but &value is an rvalue.

However, a pointer to a pointer can be set to null:

```C++
int** ptrptr { nullptr }; // use 0 instead prior to C++11
```

**Arrays of pointers**

Pointers to pointers have a few uses. The most common use is to dynamically allocate an array of pointers:

```C++
int** array { new int*[10] }; // allocate an array of 10 int pointers
```

This works just like a standard dynamically allocated array, except the array elements are of type “pointer to integer” instead of integer.

Another common use for pointers to pointers is to facilitate dynamically allocated multidimensional arrays.

Unlike a two dimensional fixed array, which can easily be declared like this:

```C++
int array[10][5];
```

Dynamically allocating a two-dimensional array is a little more challenging. You may be tempted to try something like this:

```C++
int** array { new int[10][5] }; // won’t work!
```

There are two possible solutions here. If the rightmost array dimension is a compile-time constant, you can do this:

```C++
int (*array)[5] { new int[10][5] };
```

The parenthesis are required here to ensure proper precedence. In C++11 or newer, this is a good place to use automatic type deduction:

```C++
auto array { new int[10][5] }; // so much simpler!
```

Unfortunately, this relatively simple solution doesn’t work if any non-leftmost array dimension isn’t a compile-time constant. In that case, we have to get a little more complicated. First, we allocate an array of pointers (as per above). Then we iterate through the array of pointers and allocate a dynamic array for each array element. Our dynamic two-dimensional array is a dynamic one-dimensional array of dynamic one-dimensional arrays!

```C++
int** array { new int*[10] }; // allocate an array of 10 int pointers — these are our rows
for (int count = 0; count < 10; ++count)
    array[count] = new int[5]; // these are our columns
```

We can then access our array like usual:

```C++
array[9][4] = 3; // This is the same as (array[9])[4] = 3;
```

With this method, because each array column is dynamically allocated independently, it’s possible to make dynamically allocated two dimensional arrays that are not rectangular. For example, we can make a triangle-shaped array:

```C++
int** array { new int*[10] }; // allocate an array of 10 int pointers — these are our rows
for (int count = 0; count < 10; ++count)
    array[count] = new int[count+1]; // these are our columns
```

In the above example, note that array[0] is an array of length 1, array[1] is an array of length 2, etc…

Deallocating a dynamically allocated two-dimensional array using this method requires a loop as well:

```C++
for (int count = 0; count < 10; ++count)
    delete[] array[count];
delete[] array; // this needs to be done last
```

Note that we delete the array in the opposite order that we created it (elements first, then the array itself). If we delete array before the array elements, then we’d have to access deallocated memory to delete the array elements. And that would result in undefined behavior.

Because allocating and deallocating two-dimensional arrays is complex and easy to mess up, it’s often easier to “flatten” a two-dimensional array (of size x by y) into a one-dimensional array of size x * y:

```C++
// Instead of this:
int** array { new int*[10] }; // allocate an array of 10 int pointers — these are our rows
for (int count = 0; count < 10; ++count)
    array[count] = new int[5]; // these are our columns
 
// Do this
int *array { new int[50] }; // a 10x5 array flattened into a single array
```

Simple math can then be used to convert a row and column index for a rectangular two-dimensional array into a single index for a one-dimensional array:

```C++
int getSingleIndex(int row, int col, int numberOfColumnsInArray)
{
     return (row * numberOfColumnsInArray) + col;
}
 
// set array[9,4] to 3 using our flattened array
array[getSingleIndex(9, 4, 5)] = 3;
```

**Passing a pointer by address**

Much like we can use a pointer parameter to change the actual value of the underlying argument passed in, we can pass a pointer to a pointer to a function and use that pointer to change the value of the pointer it points to (confused yet?).

However, if we want a function to be able to modify what a pointer argument points to, this is generally better done using a reference to a pointer instead. So we won’t talk about it further here.

We’ll talk more about pass by address and pass by reference in the next chapter.

**Pointer to a pointer to a pointer to…**

It’s also possible to declare a pointer to a pointer to a pointer:

```C++
int*** ptrx3{null_ptr};
```

This can be used to dynamically allocate a three-dimensional array. However, doing so would require a loop inside a loop, and is extremely complicated to get correct.

You can even declare a pointer to a pointer to a pointer to a pointer:

```C++
int**** ptrx4;
```

Or higher, if you wish.

However, in reality these don’t see much use because it’s not often you need so much indirection.

**Conclusion**

We recommend avoiding using pointers to pointers unless no other options are available, because they’re complicated to use and potentially dangerous. It’s easy enough to perform indirection through a null or dangling pointer with normal pointers — it’s doubly easy with a pointer to a pointer since you have to do a double-indirection to get to the underlying value!

### 10.22 An Introduction to `std::array`

In previous lessons, we’ve talked at length about fixed and dynamic arrays. Although both are built right into the C++ language, they both have downsides: Fixed arrays decay into pointers, losing the array length information when they do, and dynamic arrays have messy deallocation issues and are challenging to resize without error.

To address these issues, the C++ standard library includes functionality that makes array management easier, `std::array` and `std::vector`. We’ll examine `std::array` in this lesson, and `std::vector` in the next.

**An introduction to `std::array`**

`std::array` provides fixed array functionality that won’t decay when passed into a function. `std::array` is defined in the <array> header, inside the `std` namespace.

Declaring a `std::array` variable is easy:

```C++
#include <array>
 
std::array<int, 3> myArray; // declare an integer array with length 3
```

Just like the native implementation of fixed arrays, the length of a `std::array` must be known at compile time.

`std::array` can be initialized using initializer lists or list initialization:

```C++
std::array<int, 5> myArray = { 9, 7, 5, 3, 1 }; // initializer list
std::array<int, 5> myArray2 { 9, 7, 5, 3, 1 }; // list initialization
```

Unlike built-in fixed arrays, with std::array you can not omit the array length when providing an initializer:

```C++
std::array<int, > myArray { 9, 7, 5, 3, 1 }; // illegal, array length must be provided
std::array<int> myArray { 9, 7, 5, 3, 1 }; // illegal, array length must be provided
```

However, since C++17, it is allowed to omit the type and size. They can only be omitted together, but not one or the other, and only if the array is explicitly initialized.

```C++
std::array myArray { 9, 7, 5, 3, 1 }; // The type is deduced to std::array<int, 5>
std::array myArray { 9.7, 7.31 }; // The type is deduced to std::array<double, 2>
```

We favor this syntax rather than typing out the type and size at the declaration. If your compiler is not C++17 capable, you need to use the explicit syntax instead.

```C++
// std::array myArray { 9, 7, 5, 3, 1 }; // Since C++17
std::array<int, 5> myArray { 9, 7, 5, 3, 1 }; // Before C++17
 
// std::array myArray { 9.7, 7.31 }; // Since C++17
std::array<double, 2> myArray { 9.7, 7.31 }; // Before C++17
```

Since C++20, it is possible to specify the element type but omit the array length. This makes creation of `std::array` a little more like creation of C-style arrays. To create an array with a specific type and deduced size, we use the `std::to_array` function:

```C++
auto myArray1 { std::to_array<int, 5>({ 9, 7, 5, 3, 1 }) }; // Specify type and size
auto myArray2 { std::to_array<int>({ 9, 7, 5, 3, 1 }) }; // Specify type only, deduce size
auto myArray3 { std::to_array({ 9, 7, 5, 3, 1 }) }; // Deduce type and size
```

Unfortunately, `std::to_array` is more expensive than creating a `std::array` directly, because it actually copies all elements from a C-style array to a `std::array`. For this reason, `std::to_array` should be avoided when the array is created many times (e.g. in a loop).

You can also assign values to the array using an initializer list:

```C++
std::array<int, 5> myArray;
myArray = { 0, 1, 2, 3, 4 }; // okay
myArray = { 9, 8, 7 }; // okay, elements 3 and 4 are set to zero!
myArray = { 0, 1, 2, 3, 4, 5 }; // not allowed, too many elements in initializer list!
```

Accessing `std::array` values using the subscript operator works just like you would expect:

```C++
std::cout << myArray[1] << '\n';
myArray[2] = 6;
```

Just like built-in fixed arrays, the subscript operator does not do any bounds-checking. If an invalid index is provided, bad things will probably happen.

`std::array` supports a second form of array element access (the `at()` function) that does bounds checking:

```C++
std::array myArray { 9, 7, 5, 3, 1 };
myArray.at(1) = 6; // array element 1 is valid, sets array element 1 to value 6
myArray.at(9) = 10; // array element 9 is invalid, will throw a runtime error
```

In the above example, the call to `myArray.at(1)` checks to ensure the index 1 is valid, and because it is, it returns a reference to array element 1. We then assign the value of 6 to this. However, the call to `myArray.at(9)` fails because array element 9 is out of bounds for the array. Instead of returning a reference, the `at()` function throws an error that terminates the program (note: It’s actually throwing an exception of type `std::out_of_range` -- we cover exceptions in chapter 14). Because it does bounds checking, `at()` is slower (but safer) than `operator[]`.

`std::array` will clean up after itself when it goes out of scope, so there’s no need to do any kind of manual cleanup.

**Size and sorting**

The `size()` function can be used to retrieve the length of the `std::array`:

```C++
std::array myArray { 9.0, 7.2, 5.4, 3.6, 1.8 };
std::cout << "length: " << myArray.size() << '\n';
```

Because `std::array` doesn’t decay to a pointer when passed to a function, the `size()` function will work even if you call it from within a function:

```C++
#include <array>
#include <iostream>
 
void printLength(const std::array<double, 5> &myArray)
{
    std::cout << "length: " << myArray.size() << '\n';
}
 
int main()
{
    std::array myArray { 9.0, 7.2, 5.4, 3.6, 1.8 };
 
    printLength(myArray);
 
    return 0;
}
```

Note that the standard library uses the term “size” to mean the array length — do not get this confused with the results of `sizeof()` on a native fixed array, which returns the actual size of the array in memory (the size of an element multiplied by the array length). Yes, this nomenclature is inconsistent.

Also note that we passed `std::array` by (`const`) reference. This is to prevent the compiler from making a copy of the `std::array` when the `std::array` was passed to the function (for performance reasons).

**Best practice**: Always pass `std::array` by reference or `const` reference

Because the length is always known, range-based for-loops work with `std::array`:

```C++
std::array myArray{ 9, 7, 5, 3, 1 };
 
for (int element : myArray)
    std::cout << element << ' ';
```

You can sort `std::array` using `std::sort`, which lives in the <algorithm> header:

```C++
#include <algorithm> // for std::sort
#include <array>
#include <iostream>
 
int main()
{
    std::array myArray { 7, 3, 1, 9, 5 };
    std::sort(myArray.begin(), myArray.end()); // sort the array forwards
//  std::sort(myArray.rbegin(), myArray.rend()); // sort the array backwards
 
    for (int element : myArray)
        std::cout << element << ' ';
 
    std::cout << '\n';
 
    return 0;
}
```

The sorting function uses iterators, which is a concept we haven’t covered yet, so for now you can treat the parameters to `std::sort()` as a bit of magic. We’ll explain them later.

**Passing std::array of different lengths to a function**

With a std::array, the element type and array length are part of the type information. Therefore, when we use a std::array as a function parameter, we have to specify the element type and array length:

```C++
#include <array>
#include <iostream>
 
void printArray(const std::array<int, 5>& myArray)
{
    for (auto element : myArray)
        std::cout << element << ' ';
    std::cout << '\n';
}
 
int main()
{
    std::array myArray5{ 9.0, 7.2, 5.4, 3.6, 1.8 };
    printArray(myArray5);
 
    return 0;
}
```

The downside is that this limits our function to only handling arrays of this specific type and length. But what if we want to have our function handle arrays of different element types or lengths? We’d have to create a copy of the function for each different element type and/or array length we want to use. That’s a lot of duplication.

Fortunately, we can have C++ do this for us, using templates. We can create a template function that parameterizes part or all of the type information, and then C++ will use that template to create “real” functions (with actual types) as needed.

```C++
#include <array>
#include <cstddef>
#include <iostream>
 
// printArray is a template function
template <typename T, std::size_t size> // parameterize the element type and size
void printArray(const std::array<T, size>& myArray)
{
    for (auto element : myArray)
        std::cout << element << ' ';
    std::cout << '\n';
}
 
int main()
{
    std::array myArray5{ 9.0, 7.2, 5.4, 3.6, 1.8 };
    printArray(myArray5);
 
    std::array myArray7{ 9.0, 7.2, 5.4, 3.6, 1.8, 1.2, 0.7 };
    printArray(myArray7);
 
    return 0;
}
```





### 10.23 An Introduction to `std::vector`

In the previous lesson, we introduced `std::array`, which provides the functionality of C++’s built-in fixed arrays in a safer and more usable form.

Analogously, the C++ standard library provides functionality that makes working with dynamic arrays safer and easier. This functionality is named `std::vector`.

Unlike `std::array`, which closely follows the basic functionality of fixed arrays, `std::vector` comes with some additional tricks up its sleeves. These help make `std::vector` one of the most useful and versatile tools to have in your C++ toolkit.

**An introduction to std::vector**

Introduced in C++03, `std::vector` provides dynamic array functionality that handles its own memory management. This means you can create arrays that have their length set at run-time, without having to explicitly allocate and deallocate memory using `new` and `delete`. `std::vector` lives in the <vector> header.

Declaring a `std::vector` is simple:

```C++
#include <vector>
 
// no need to specify length at the declaration
std::vector<int> array; 
std::vector<int> array2 = { 9, 7, 5, 3, 1 }; // use initializer list to initialize array (Before C++11)
std::vector<int> array3 { 9, 7, 5, 3, 1 }; // use uniform initialization to initialize array
 
// as with std::array, the type can be omitted since C++17
std::vector array4 { 9, 7, 5, 3, 1 }; // deduced to std::vector<int>
```

Note that in both the uninitialized and initialized case, you do not need to include the array length at compile time. This is because `std::vector` will dynamically allocate memory for its contents as requested.

Just like `std::array`, accessing array elements can be done via the `[]` operator (which does no bounds checking) or the `at()` function (which does bounds checking):

```C++
array[6] = 2; // no bounds checking
array.at(7) = 3; // does bounds checking
```

In either case, if you request an element that is off the end of the array, the vector will *not* automatically resize.

As of C++11, you can also assign values to a `std::vector` using an initializer-list:

```C++
array = { 0, 1, 2, 3, 4 }; // okay, array length is now 5
array = { 9, 8, 7 }; // okay, array length is now 3
```

In this case, the vector will self-resize to match the number of elements provided.

**Self-cleanup prevents memory leaks**

When a vector variable goes out of scope, it automatically deallocates the memory it controls (if necessary). This is not only handy (as you don’t have to do it yourself), it also helps prevent memory leaks. Consider the following snippet:

```C++
void doSomething(bool earlyExit)
{
    int* array{ new int[5] { 9, 7, 5, 3, 1 } }; // allocated memory using new
 
    if (earlyExit)
        return; // exits the function without deallocating the memory allocated above
 
    // do stuff here
 
    delete[] array; // never called
}
```

If earlyExit is set to true, array will never be deallocated, and the memory will be leaked.

However, if `array` is a `std::vector`, this won’t happen, because the memory will be deallocated as soon as `array` goes out of scope (regardless of whether the function exits early or not). This makes `std::vector` much safer to use than doing your own memory allocation.

**Vectors remember their length**

Unlike built-in dynamic arrays, which don’t know the length of the array they are pointing to, std::vector keeps track of its length. We can ask for the vector’s length via the `size()` function:

```C++
#include <iostream>
#include <vector>
 
void printLength(const std::vector<int>& array)
{
    std::cout << "The length is: " << array.size() << '\n';
}
 
int main()
{
    std::vector array { 9, 7, 5, 3, 1 };
    printLength(array);
 
    return 0;
}
```

Just like with `std::array`, `size()` returns a value of nested type `size_type` (full type in the above example would be `std::vector<int>::size_type`), which is an unsigned integer.

**Resizing a vector**

Resizing a built-in dynamically allocated array is complicated. Resizing a `std::vector` is as simple as calling the `resize()` function:

```C++
#include <iostream>
#include <vector>
 
int main()
{
    std::vector array { 0, 1, 2 };
    array.resize(5); // set size to 5
 
    std::cout << "The length is: " << array.size() << '\n';
 
    for (int i : array)
        std::cout << i << ' ';
 
    std::cout << '\n';
 
    return 0;
}
```

There are two things to note here. First, when we resized the vector, the existing element values were preserved! Second, new elements are initialized to the default value for the type (which is 0 for integers).

Vectors may be resized to be smaller:

```C++
#include <vector>
#include <iostream>
 
int main()
{
    std::vector array { 0, 1, 2, 3, 4 };
    array.resize(3); // set length to 3
 
    std::cout << "The length is: " << array.size() << '\n';
 
    for (int i : array)
        std::cout << i << ' ';
 
    std::cout << '\n';
 
    return 0;
}
```

Resizing a vector is computationally expensive, so you should strive to minimize the number of times you do so. If you need a vector with a specific number of elements but don’t know the values of the elements at the point of declaration, you can create a vector with default elements like so:

```C++
#include <iostream>
#include <vector>
 
int main()
{
    // Using direct initialization, we can create a vector with 5 elements,
    // each element is a 0. If we use brace initialization, the vector would
    // have 1 element, a 5.
    std::vector<int> array(5);
 
    std::cout << "The length is: " << array.size() << '\n';
 
    for (int i : array)
        std::cout << i << ' ';
 
    std::cout << '\n';
 
    return 0;
}
```



We’ll talk about why direct and brace-initialization are treated differently in lesson 16.7 -- std::initializer_list. A rule of thumb is, if the type is some kind of list and you don’t want to initialize it with a list, use direct initialization.

**Compacting bools**

`std::vector` has another cool trick up its sleeves. There is a special implementation for `std::vector` of type bool that will compact 8 booleans into a byte! This happens behind the scenes, and doesn’t change how you use the std::vector.

```C++
#include <vector>
#include <iostream>
 
int main()
{
    std::vector<bool> array { true, false, false, true, true };
    std::cout << "The length is: " << array.size() << '\n';
 
    for (int i : array)
        std::cout << i << ' ';
 
    std::cout << '\n';
 
    return 0;
}
```

**More to come**

Note that this is an introduction article intended to introduce the basics of `std::vector`. In lesson 11.9 -- std::vector capacity and stack behavior, we’ll cover some additional capabilities of `std::vector`, including the difference between a vector’s length and capacity, and take a deeper look into how `std::vector` handles memory allocation.

**Conclusion**

Because variables of type `std::vector` handle their own memory management (which helps prevent memory leaks), remember their length, and can be easily resized, we recommend using `std::vector` in most cases where dynamic arrays are needed.

### 10.24 Introduction to Iterators

Iterating through an array (or other structure) of data is quite a common thing to do in programming. And so far, we’ve covered many different ways to do so: with loops and an index (`for-loops` and `while loops`), with pointers and pointer arithmetic, and with `range-based for-loops`:





**Iterators**

An **iterator** is an object designed to traverse through a container (e.g. the values in an array, or the characters in a string), providing access to each element along the way.

A container may provide different kinds of iterators. For example, an array container might offer a forwards iterator that walks through the array in forward order, and a reverse iterator that walks through the array in reverse order.

Once the appropriate type of iterator is created, the programmer can then use the interface provided by the iterator to traverse and access elements without having to worry about what kind of traversal is being done or how the data is being stored in the container. And because C++ iterators typically use the same interface for traversal (operator++ to move to the next element) and access (operator* to access the current element), we can iterate through a wide variety of different container types using a consistent method.

**Pointers as an iterator**

The simplest kind of iterator is a pointer, which (using pointer arithmetic) works for data stored sequentially in memory. Let’s revisit a simple array traversal using a pointer and pointer arithmetic:

```C++
#include <array>
#include <iostream>
 
int main()
{
    std::array data{ 0, 1, 2, 3, 4, 5, 6 };
 
    auto begin{ &data[0] };
    // note that this points to one spot beyond the last element
    auto end{ begin + std::size(data) };
 
    // for-loop with pointer
    for (auto ptr{ begin }; ptr != end; ++ptr) // ++ to move to next element
    {
        std::cout << *ptr << ' '; // Indirection to get value of current element
    }
    std::cout << '\n';
 
    return 0;
}
```

In the above, we defined two variables: `begin` (which points to the beginning of our container), and `end` (which marks an end point). For arrays, the end marker is typically the place in memory where the last element would be if the container contained one more element.

The pointer then iterates between `begin` and `end`, and the current element can be accessed by indirection through the pointer.

**Standard library iterators**

Iterating is such a common operation that all standard library containers offer direct support for iteration. Instead of calculating our own begin and end points, we can simply ask the container for the begin and end points via functions conveniently named `begin()` and `end()`:

```C++
#include <array>
#include <iostream>
 
int main()
{
    std::array array{ 1, 2, 3 };
 
    // Ask our array for the begin and end points (via the begin and end member functions).
    auto begin{ array.begin() };
    auto end{ array.end() };
 
    for (auto p{ begin }; p != end; ++p) // ++ to move to next element.
    {
        std::cout << *p << ' '; // Indirection to get value of current element.
    }
    std::cout << '\n';
 
    return 0;
}
```

The `iterator` header also contains two generic functions (`std::begin` and `std::end`) that can be used:

```C++
#include <array>
#include <iostream>
#include <iterator> // For std::begin and std::end
 
int main()
{
    std::array array{ 1, 2, 3 };
 
    // Use std::begin and std::end to get the begin and end points.
    auto begin{ std::begin(array) };
    auto end{ std::end(array) };
 
    for (auto p{ begin }; p != end; ++p) // ++ to move to next element
    {
        std::cout << *p << ' '; // Indirection to get value of current element
    }
    std::cout << '\n';
 
    return 0;
}
```

Don’t worry about the types of the iterators for now, we’ll re-visit iterators in a later chapter. The important thing is that the iterator takes care of the details of iterating through the container. All we need are four things: the begin point, the end point, operator++ to move the iterator to the next element (or the end), and operator* to get the value of the current element.

**Back to range-based for loops**

All types that have `begin` and `end` member functions or can be used with `std::begin` and `std::end` are usable in range-based for-loops.

```C++
#include <array>
#include <iostream>
 
int main()
{
    std::array array{ 1, 2, 3 };
 
    // This does exactly the same as the loop we used before.
    for (int i : array)
    {
        std::cout << i << ' ';
    }
    std::cout << '\n';
 
    return 0;
}
```

Behind the scenes, the range-based for-loop calls `begin()` and `end()` of the type to iterate over. `std::array` has `begin` and `end` member functions, so we can use it in a range-based loop. C-style fixed arrays can be used with `std::begin` and `std::end` functions, so we can loop through them with a range-based loop as well. Dynamic arrays don’t work though, because there is no `std::end` function for them (because the type information doesn’t contain the array’s length).

You’ll learn how to add functions to your types later, so that they can be used with range-based for-loops too.

Range-based for-loops aren’t the only thing that makes use of iterators. They’re also used in `std::sort` and other algorithms. Now that you know what they are, you’ll notice they’re used quite a bit in the standard library.

**Iterator invalidation (dangling iterators)**

Much like pointers and references, iterators can be left “dangling” if the elements being iterated over change address or are destroyed. When this happens, we say the iterator has been **invalidated**. Accessing an invalidated iterator produces undefined behavior.

Some operations that modify containers (such as adding an element to a `std::vector`) can have the side effect of causing the elements in the container to change addresses. When this happens, existing iterators to those elements will be invalidated. Good C++ reference documentation should note which container operations may or will invalidate iterators. As an example, see the [“Iterator invalidation” section of `std::vector` on cppreference](https://en.cppreference.com/w/cpp/container/vector#Iterator_invalidation).

Here’s an example of this:

```C++
#include <iostream>
#include <vector>
 
int main()
{
	std::vector v{ 1, 2, 3, 4, 5, 6, 7 };
 
	auto it{ v.begin() };
 
	++it; // move to second element
	std::cout << *it << '\n'; // ok: prints 2
 
	v.erase(it); // erase the element currently being iterated over
 
	// erase() invalidates iterators to the erased element (and subsequent elements)
	// so iterator "it" is now invalidated
 
	++it; // undefined behavior
	std::cout << *it << '\n'; // undefined behavior
 
	return 0;
}
```

### 10.25 Introduction to Standard Library Algorithm

New programmers typically spend a lot of time writing custom loops to perform relatively simple tasks, such as sorting or counting or searching arrays. These loops can be problematic, both in terms of how easy it is to make an error, and in terms of overall maintainability, as loops can be hard to understand.

Because searching, counting, and sorting are such common operations to do, the C++ standard library comes with a bunch of functions to do these things in just a few lines of code. Additionally, these standard library functions come pre-tested, are efficient, work on a variety of different container types, and many support parallelization (the ability to devote multiple CPU threads to the same task in order to complete it faster).

The functionality provided in the algorithms library generally fall into one of three categories:

- **Inspectors** -- Used to view (but not modify) data in a container. Examples include searching and counting.
- **Mutators** -- Used to modify data in a container. Examples include sorting and shuffling.
- **Facilitators** -- Used to generate a result based on values of the data members. Examples include objects that multiply values, or objects that determine what order pairs of elements should be sorted in.

These algorithms live in the [algorithms](https://en.cppreference.com/w/cpp/algorithm) library. In this lesson, we’ll explore some of the more common algorithms -- but there are many more, and we encourage you to read through the linked reference to see everything that’s available!

**Using std::find to find an element by value**

[`std::find`](https://en.cppreference.com/w/cpp/algorithm/find) searches for the first occurrence of a value in a container. `std::find` takes 3 parameters: an iterator to the starting element in the sequence, an iterator to the ending element in the sequence, and a value to search for. It returns an iterator pointing to the element (if it is found) or the end of the container (if the element is not found).

For example:

```C++
#include <algorithm>
#include <array>
#include <iostream>
 
int main()
{
    std::array arr{ 13, 90, 99, 5, 40, 80 };
 
    std::cout << "Enter a value to search for and replace with: ";
    int search{};
    int replace{};
    std::cin >> search >> replace;
 
    // Input validation omitted
 
    // std::find returns an iterator pointing to the found element (or the end of the container)
    // we'll store it in a variable, using type inference to deduce the type of
    // the iterator (since we don't care)
    auto found{ std::find(arr.begin(), arr.end(), search) };
 
    // Algorithms that don't find what they were looking for return the end iterator.
    // We can access it by using the end() member function.
    if (found == arr.end())
    {
        std::cout << "Could not find " << search << '\n';
    }
    else
    {
        // Override the found element.
        *found = replace;
    }
 
    for (int i : arr)
    {
        std::cout << i << ' ';
    }
 
    std::cout << '\n';
 
    return 0;
}
```

**Using std::find_if to find an element that matches some condition**

Sometimes we want to see if there is a value in a container that matches some condition (e.g. a string that contains a specific substring) rather than an exact value. In such cases, `std::find_if` is perfect. The `std::find_if` function works similarly to `std::find`, but instead of passing in a value to search for, we pass in a callable object, such as a function pointer (or a lambda, which we’ll cover later) that checks to see if a match is found. `std::find_if` will call this function for every element until a matching element is found (or no more elements remain in the container to check).

Here’s an example where we use `std::find_if` to check if any elements contain the substring “nut”:

```C++
#include <algorithm>
#include <array>
#include <iostream>
#include <string_view>
 
// Our function will return true if the element matches
bool containsNut(std::string_view str)
{
    // std::string_view::find returns std::string_view::npos if it doesn't find
    // the substring. Otherwise it returns the index where the substring occurs
    // in str.
    return (str.find("nut") != std::string_view::npos);
}
 
int main()
{
    std::array<std::string_view, 4> arr{ "apple", "banana", "walnut", "lemon" };
 
    // Scan our array to see if any elements contain the "nut" substring
    auto found{ std::find_if(arr.begin(), arr.end(), containsNut) };
 
    if (found == arr.end())
    {
        std::cout << "No nuts\n";
    }
    else
    {
        std::cout << "Found " << *found << '\n';
    }
 
    return 0;
}
```

If you were to write the above example by hand, you’d need at least three loops (one to loop through the array, and two to match the substring). The standard library functions allow us to do the same thing in just a few lines of code!

**Using std::count and std::count_if to count how many occurrences there are**

[`std::count`](https://en.cppreference.com/w/cpp/algorithm/count) and `std::count_if` search for all occurrences of an element or an element fulfilling a condition.

In the following example, we’ll count how many elements contain the substring “nut”:

```C++
#include <algorithm>
#include <array>
#include <iostream>
#include <string_view>
 
bool containsNut(std::string_view str)
{
	return (str.find("nut") != std::string_view::npos);
}
 
int main()
{
	std::array<std::string_view, 5> arr{ "apple", "banana", "walnut", "lemon", "peanut" };
 
	auto nuts{ std::count_if(arr.begin(), arr.end(), containsNut) };
 
	std::cout << "Counted " << nuts << " nut(s)\n";
 
	return 0;
}
```

**Using std::sort to custom sort**

We previously used [`std::sort`](https://en.cppreference.com/w/cpp/algorithm/sort) to sort an array in ascending order, but std::sort can do more than that. There’s a version of `std::sort` that takes a function as its third parameter that allows us to sort however we like. The function takes two parameters to compare, and returns true if the first argument should be ordered before the second. By default, `std::sort` sorts the elements in ascending order.

Let’s use `std::sort` to sort an array in reverse order using a custom comparison function named `greater`:

```C++
#include <algorithm>
#include <array>
#include <iostream>
 
bool greater(int a, int b)
{
    // Order @a before @b if @a is greater than @b.
    return (a > b);
}
 
int main()
{
    std::array arr{ 13, 90, 99, 5, 40, 80 };
 
    // Pass greater to std::sort
    std::sort(arr.begin(), arr.end(), greater);
 
    for (int i : arr)
    {
        std::cout << i << ' ';
    }
 
    std::cout << '\n';
 
    return 0;
}
```

Once again, instead of writing our own custom loop functions, we can sort our array however we like in just a few lines of code!

Our `greater` function needs 2 arguments, but we’re not passing it any, so where do they come from? When we use a function without parentheses (), it’s only a function pointer, not a call. You might remember this from when we tried to print a function without parentheses and `std::cout` printed “1”. `std::sort` uses this pointer and calls the actual `greater` function with any 2 elements of the array. We don’t know which elements `greater` will be called with, because it’s not defined which sorting algorithm `std::sort` is using under the hood. We talk more about function pointers in a later chapter.

Tip

Because sorting in descending order is so common, C++ provides a custom type (named `std::greater`) for that too (which is part of the [functional](https://en.cppreference.com/w/cpp/header/functional) header). In the above example, we can replace:

```C++
std::sort(arr.begin(), arr.end(), greater); // call our custom greater function
```

with:

```C++
std::sort(arr.begin(), arr.end(), std::greater{}); // use the standard library greater comparison
  // Before C++17, we had to specify the element type when we create std::greater
  std::sort(arr.begin(), arr.end(), std::greater<int>{}); // use the standard library greater comparison
```

Note that the `std::greater{}` needs the curly braces because it is not a callable function. It’s a type, and in order to use it, we need to instantiate an object of that type. The curly braces instantiate an anonymous object of that type (which then gets passed as an argument to std::sort).





**Conclusion**

The algorithms library has a ton of useful functionality that can make your code simpler and more robust. We only cover a small subset in this lesson, but because most of these functions work very similarly, once you know how a few work, you can make use of most of them.

**Best practice**: Favor using functions from the algorithms library over writing your own functionality to do the same thing.
