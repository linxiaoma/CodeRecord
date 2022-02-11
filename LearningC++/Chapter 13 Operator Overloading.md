# Chapter 13 Operator Overloading

### 13.1 Introduction to Operator Overloading

In C++, operators are implemented as functions. By using function overloading on the operator functions, you can define your own versions of the operators that work with different data types (including classes that you’ve written). Using function overloading to overload operators is called **operator overloading**.

**Operators as functions**

Consider the following example:

```C++
int x = 2;
int y = 3;
std::cout << x + y << '\n';
```

The compiler comes with a built-in version of the plus operator (+) for integer operands -- this function adds integers x and y together and returns an integer result. When you see the expression `x + y`, you can translate this in your head to the function call `operator+(x, y)` (where operator+ is the name of the function). The compiler also comes with a built-in version of the plus operator (+) for double operands.

**Resolving overloaded operators**

When evaluating an expression containing an operator, the compiler uses the following rules:

- If *all* of the operands are fundamental data types, the compiler will call a built-in routine if one exists. If one does not exist, the compiler will produce a compiler error.
- If *any* of the operands are user data types (e.g. one of your classes, or an enum type), the compiler looks to see whether the type has a matching overloaded operator function that it can call. If it can’t find one, it will try to convert one or more of the user-defined type operands into fundamental data types so it can use a matching built-in operator (via an overloaded typecast, which we’ll cover later in this chapter). If that fails, then it will produce a compile error.

**What are the limitations on operator overloading?**

First, almost any existing operator in C++ can be overloaded. The exceptions are: conditional (?:), sizeof, scope (::), member selector (.), member pointer selector (.*), typeid, and the casting operators.

Second, you can only overload the operators that exist. You can not create new operators or rename existing operators. For example, you could not create an operator ** to do exponents.

Third, at least one of the operands in an overloaded operator must be a user-defined type. This means you can not overload the plus operator to work with one integer and one double. However, you could overload the plus operator to work with an integer and a Mystring.

Fourth, it is not possible to change the number of operands an operator supports.

Finally, all operators keep their default precedence and associativity (regardless of what they’re used for) and this can not be changed.

**Best practice**

- When overloading operators, it’s best to keep the function of the operators as close to the original intent of the operators as possible.
- If the meaning of an overloaded operator is not clear and intuitive, use a named function instead.

### 13.2 Overloading the Arithmetic Operator using Friend Functions

It turns out that there are three different ways to overload operators: **the member function way**, **the friend function way**, and **the normal function way**. 

Friend functions can be defined inside the class

Even though friend functions are not members of the class, they can still be defined inside the class if desired.



**Overloading operators for operands of different types**

Often it is the case that you want your overloaded operators to work with operands that are different types. For example, if we have Cents(4), we may want to add the integer 6 to this to produce the result Cents(10).

When C++ evaluates the expression `x + y`, x becomes the first parameter, and y becomes the second parameter. When x and y have the same type, it does not matter if you add x + y or y + x -- either way, the same version of operator+ gets called. However, when the operands have different types, x + y does not call the same function as y + x.

For example, `Cents(4) + 6` would call operator+(Cents, int), and `6 + Cents(4)` would call operator+(int, Cents). Consequently, whenever we overload binary operators for operands of different types, we actually need to write two functions -- one for each case.

It is often possible to define overloaded operators by calling other overloaded operators. You should do so if and when doing so produces simpler code. In cases where the implementation is trivial (e.g. a single line) it’s often not worth doing this, as the added indirection of an additional function call is more complicated than just implementing the function directly.

### 13.3 Overloading Operators Using Normal Functions

In general, a normal function should be preferred over a friend function if it’s possible to do so with the existing member functions available (the less functions touching your classes’s internals, the better). However, don’t add additional access functions just to overload an operator as a normal function instead of a friend function!

**Best practice**: Prefer overloading operators as normal functions instead of friends if it’s possible to do so without adding additional functions.

### 13.4 Overloading the I/O Operators

For classes that have multiple member variables, printing each of the individual variables on the screen can get tiresome fast.

**Overloading operator<<**

Overloading operator<< is similar to overloading operator+ (they are both binary operators), except that the parameter types are different.

Consider the expression `std::cout << point`. If the operator is <<, what are the operands? The left operand is the std::cout object, and the right operand is your Point class object. std::cout is actually an object of type std::ostream. Therefore, our overloaded function will look like this:

```C++
// std::ostream is the type for object std::coutfriend std::ostream& operator<< (std::ostream& out, const Point& point);
```

Implementation of operator<< for our Point class is fairly straightforward -- because C++ already knows how to output doubles using operator<<, and our members are all doubles, we can simply use operator<< to output the member variables of our Point. Here is the above Point class with the overloaded operator<<.

```C++
#include <iostream>
 
class Point
{
private:
    double m_x{};
    double m_y{};
    double m_z{};
 
public:
    Point(double x=0.0, double y=0.0, double z=0.0)
      : m_x{x}, m_y{y}, m_z{z}
    {
    }
 
    friend std::ostream& operator<< (std::ostream& out, const Point& point);
};
 
std::ostream& operator<< (std::ostream& out, const Point& point)
{
    // Since operator<< is a friend of the Point class, we can access Point's members directly.
    out << "Point(" << point.m_x << ", " << point.m_y << ", " << point.m_z << ')'; // actual output done here
 
    return out; // return std::ostream so we can chain calls to operator<<
}
 
int main()
{
    const Point point1{2.0, 3.0, 4.0};
 
    std::cout << point1 << '\n';
 
    return 0;
}
```

**Overloading operator>>**

It is also possible to overload the input operator. This is done in a manner analogous to overloading the output operator. The key thing you need to know is that std::cin is an object of type std::istream. Here’s our Point class with an overloaded operator>>:

```C++
#include <iostream> class Point{private:    double m_x{};    double m_y{};    double m_z{}; public:    Point(double x=0.0, double y=0.0, double z=0.0)      : m_x{x}, m_y{y}, m_z{z}    {    }     friend std::ostream& operator<< (std::ostream& out, const Point& point);    friend std::istream& operator>> (std::istream& in, Point& point);}; std::ostream& operator<< (std::ostream& out, const Point& point){    // Since operator<< is a friend of the Point class, we can access Point's members directly.    out << "Point(" << point.m_x << ", " << point.m_y << ", " << point.m_z << ')';     return out;} std::istream& operator>> (std::istream& in, Point& point){    // Since operator>> is a friend of the Point class, we can access Point's members directly.    // note that parameter point must be non-const so we can modify the class members with the input values    in >> point.m_x;    in >> point.m_y;    in >> point.m_z;     return in;}
```

**Conclusion**

Overloading operator<< and operator>> make it extremely easy to output your class to screen and accept user input from the console.

### 13.5 Overloading Operators using Member Functions

Overloading operators using a member function is very similar to overloading operators using a friend function. When overloading an operator using a member function:

- The overloaded operator must be added as a member function of the left operand.
- The left operand becomes the implicit *this object
- All other operands become function parameters.

Converting a friend overloaded operator to a member overloaded operator is easy:

1. The overloaded operator is defined as a member instead of a friend (Cents::operator+ instead of friend operator+)
2. The left parameter is removed, because that parameter now becomes the implicit *this object.
3. Inside the function body, all references to the left parameter can be removed (e.g. cents.m_cents becomes m_cents, which implicitly references the *this object).

Let’s take a closer look at how the expression `cents1 + 2` evaluates.

In the friend function version, the expression `cents1 + 2` becomes function call operator+(cents1, 2). Note that there are two function parameters. This is straightforward.

In the member function version, the expression `cents1 + 2` becomes function call cents1.operator+(2). Note that there is now only one explicit function parameter, and cents1 has become an object prefix. However, in lesson [12.10 -- The hidden “this” pointer](https://www.learncpp.com/cpp-tutorial/the-hidden-this-pointer/), we mentioned that the compiler implicitly converts an object prefix into a hidden leftmost parameter named *this. So in actuality, cents1.operator+(2) becomes operator+(&cents1, 2), which is almost identical to the friend version.

Both cases produce the same result, just in slightly different ways.

So if we can overload an operator as a friend or a member, which should we use? In order to answer that question, there’s a few more things you’ll need to know.

**Not everything can be overloaded as a friend function**

The assignment (=), subscript ([]), function call (()), and member selection (->) operators must be overloaded as member functions, because the language requires them to be.

**Not everything can be overloaded as a member function**

In lesson [13.4 -- Overloading the I/O operators](https://www.learncpp.com/cpp-tutorial/overloading-the-io-operators/), we overloaded operator<< for our Point class using the friend function method.

However, we are not able to overload operator<< as a member function. Why not? Because the overloaded operator must be added as a member of the left operand. In this case, the left operand is an object of type std::ostream. std::ostream is fixed as part of the standard library. We can’t modify the class declaration to add the overload as a member function of std::ostream.

This necessitates that operator<< be overloaded as a normal function (preferred) or a friend.

Similarly, although we can overload operator+(Cents, int) as a member function (as we did above), we can’t overload operator+(int, Cents) as a member function, because int isn’t a class we can add members to.

Typically, we won’t be able to use a member overload if the left operand is either not a class (e.g. int), or it is a class that we can’t modify (e.g. std::ostream).

**When to use a normal, friend, or member function overload**

In most cases, the language leaves it up to you to determine whether you want to use the normal/friend or member function version of the overload. However, one of the two is usually a better choice than the other.

When dealing with binary operators that don’t modify the left operand (e.g. operator+), the normal or friend function version is typically preferred, because it works for all parameter types (even when the left operand isn’t a class object, or is a class that is not modifiable). The normal or friend function version has the added benefit of “symmetry”, as all operands become explicit parameters (instead of the left operand becoming *this and the right operand becoming an explicit parameter).

When dealing with binary operators that do modify the left operand (e.g. operator+=), the member function version is typically preferred. In these cases, the leftmost operand will always be a class type, and having the object being modified become the one pointed to by *this is natural. Because the rightmost operand becomes an explicit parameter, there’s no confusion over who is getting modified and who is getting evaluated.

Unary operators are usually overloaded as member functions as well, since the member version has no parameters.

The following rules of thumb can help you determine which form is best for a given situation:

- If you’re overloading assignment (=), subscript ([]), function call (()), or member selection (->), do so as a member function.
- If you’re overloading a unary operator, do so as a member function.
- If you’re overloading a binary operator that does not modify its left operand (e.g. operator+), do so as a normal function (preferred) or friend function.
- If you’re overloading a binary operator that modifies its left operand, but you can’t modify the definition of the left operand (e.g. operator<<, which has a left operand of type ostream), do so as a normal function (preferred) or friend function.
- If you’re overloading a binary operator that modifies its left operand (e.g. operator+=), and you can modify the definition of the left operand, do so as a member function.





































































## The End?

Congratulations! You made it all the way through the tutorials! Now, after breathing a long sigh of relief, you’re probably asking the question, “What next?”.

**What next?**

By this point, you should have a solid understanding of the core C++ language. This sets you up well to continue your learning journey into other adjacent areas. So if there’s something you’re really interested in learning about, now’s a good time to see whether you have enough knowledge to jump into that.

However, for most users, I think there are a few natural next steps.

**Data structures and algorithms**

If you haven’t already learned about these, this is my strongest recommendation.

A **data structure** is a collection of data and a well defined set of methods to access or manipulate that data. The most common data structure used in programming is the array, which holds a number of elements of the same type in sequential memory. You can manipulate the data inside an array by using array indexing to directly access (or modify) the elements inside the array. In the lessons, we also covered the stack data structure, which provide push, pop, and top functions to access the data on the stack.

An **algorithm** is a self-contained set of operations that typically manipulate or calculate outputs from the data in a data structure. For example, when you look through an array to find the median value, you’re executing an algorithm. Binary search is an algorithm to determine if a given value exists in a sorted array. Sorting routines (such as selection sort and bubble sort) are algorithms that sort data sets.

Over the years, mathematicians and computer scientists have come up with a fairly standard set of reusable data structures and algorithms that are useful for constructing more complex programs. These all have various tradeoffs. For example, arrays are fast to access data and sort, but slow to add or remove elements. Linked lists, on the other hand, are slow to access data and sort, but very fast to add or remove elements (if you already know where those elements are).

**Why does it matter?** Data structures and algorithms serve the same purpose in programming: they are tools that, if you know how to use them, can vastly accelerate how quickly you can get things done at quality.

The good news is that many of these data structures and algorithms have already been implemented in the standard library. You’ve already encountered some of these in the preceding tutorials: std::array, std::vector, std::stack, std::string, and std::sort, to name a few. Learning to use these effectively and appropriately is a great use of your time.

If you’re short on time (or patience), learning how to use the most common data structures and algorithms is the minimum you should do. But if you have the inclination, try recreating those data structures yourself, from scratch. It’s really good practice on writing reusable code, and will help you down the road when something you need isn’t in the standard library. But then throw them out, and use the ones in the standard library. :)

**The C++ standard library**

The bulk of the C++ standard library is data structures and algorithms. However, the standard library contains other things too, and another next step could be to explore those. Among other things, there are numerics (math) libraries, input/output routines, functions to handle localization and regionalization, regular expressions, threading, and file access. Every new release of C++ (which has been happening about every 3 years now) adds a batch of new functionality into the standard library. It isn’t critical that you know how everything in there works, but it’s worth at least being aware of what exists, so that if you happen upon the need for it, you can go learn more as needed. [https://cppreference.com/w/cpp](https://en.cppreference.com/w/cpp) is my go-to reference for discovering what exists.

**Graphical applications**

In our tutorial series, we developed console applications, because they’re easy, cross-platform, and don’t require installing additional software. Unlike many modern programming languages, C++ does not come with functionality to create application windows, or to populate those windows with graphical elements or graphical user interface widgets (checkboxes, sliders, etc…). To do those things in C++, you’ll need to enlist the help of a 3rd party library.

Getting a graphical application up and running requires a few additional steps. First, you’ll need to actually install the 3rd party library and connect it to your IDE, so you can compile it into your program. Most graphical libraries should come with instructions on how to do this for the most popular IDEs. Next, you need to instantiate an OS window, which requires calling certain function from the toolkit. Most, if not all, of the libraries should have sample programs that you can compile and dissect if you’re not sure how to do something basic.

There are a lot of libraries out there, and which one you should use depends on your requirements (you’ll have to do your own research to determine which one is right for you). Popular choices include Qt, WxWidgets, SDL, and SFML. If you want to do 3d graphics, all of these frameworks support OpenGL, and there are great OpenGL tutorials on the internet.

Graphical applications typically run differently than console applications. With a console application, the program starts executing at the top of main() and then runs sequentially, usually stopping only for user input. Graphical applications also start executing at the top of main(), typically spawn a window, populate it with graphics or widgets, and then go into an infinite loop waiting for the user to interact with the window (via mouse click or keyboard). This infinite loop is called an event loop, and when a click or keypress happens, that event is routed to the function(s) that handle that type of event. This is called event handling. Once the event is handled, the event loop continues to run, waiting for the next bit of user input.

**TCP/IP / Network programming (aka. the internets)**

Networking (broadly) is the concept of having your program connect to other programs, either on your machine, or on network-connected machines, to exchange information. 

As with many things C++, there are libraries out there to help make your C++ programs network capable. The Asio C++ library is a commonly used one (there are two variants -- a standalone version, and a version that integrates with Boost, which is a library that provides a lot of different functions, much like the standard library).

**Multithreading**

All of the programs we’ve seen in this tutorial series run sequentially. One task is completed, then the next one starts. If a task gets stuck (e.g. you’re asking the user for input and they haven’t entered any yet), the whole program pauses. 

Fortunately, a method exists to allow programs to execute multiple tasks at the same time. This is called threading. Much like how (most of) you can walk and chew bubble gum at the same time, threading allows a program to “split” its attention and do multiple things in parallel.

Threading is powerful, but it introduces additional complexity, and a lot of room for additional errors. Therefore, I wouldn’t recommend starting here -- but it is a good area to learn about eventually, especially if you want to do complex graphical applications or network programming.

**Improve your fundamentals**

Another option is to spend time improving your understanding of best practices. For this, I highly recommend having a read-through of [the CPP Core Guidelines](https://github.com/isocpp/CppCoreGuidelines), with an optional delving into the [GSL library](https://github.com/microsoft/GSL).

**A good bye!**

At this point, I’d like to take a moment to thank you for stopping by and reading this tutorial series. I hope you enjoyed your time here and have found this site useful. 

Good luck (and skill) in your future endeavors, and happy programming! And remember, <font color='blue'>old programmers never die -- they just go out of scope</font>.

