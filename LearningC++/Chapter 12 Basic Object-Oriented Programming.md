# Chapter 12 Basic Object-Oriented Programming

we defined an object in C++ as, “a piece of memory that can be used to store values”. An object with a name is called a variable.

In traditional programming (what we’ve been doing prior to this point), programs are basically lists of instructions to the computer that define data (via objects) and then work with that data (via statements and functions). Data and the functions that work on that data are separate entities that are combined together to produce the desired result. Because of this separation, traditional programming often does not provide a very intuitive representation of reality. It’s up to the programmer to manage and connect the properties (variables) to the behaviors (functions) in an appropriate manner. This leads to code that looks like this:

```C++
driveTo(you, work);
```

Object-oriented programming (OOP) provides us with the ability to create objects that tie together both properties and behaviors into a self-contained, reusable package. This leads to code that looks more like this:

```C++
you.driveTo(work);	
```

This not only reads more clearly, it also makes it clearer who the subject is (you) and what behavior is being invoked (driving somewhere). Rather than being focused on writing functions, we’re focused on defining objects that have a well-defined set of behaviors. This is why the paradigm is called “object-oriented”.

This allows programs to be written in a more modular fashion, which makes them easier to write and understand, and also provides a higher degree of code-reusability. These objects also provide a more intuitive way to work with our data by allowing us to define how we interact with the objects, and how they interact with other objects.

Note that OOP doesn’t replace traditional programming methods. Rather, it gives you additional tools in your programming tool belt to manage complexity when needed.

Object-oriented programming also brings several other useful concepts to the table: inheritance, encapsulation, abstraction, and polymorphism (language designers have a philosophy: never use a short word where a long one will do). 

While C++ provides a number of fundamental data types (e.g. char, int, long, float, double, etc…) that are often sufficient for solving relatively simple problems, it can be difficult to solve complex problems using just these types. One of C++’s more useful features is the ability to define your own data types that better correspond to the problem being solved. You have already seen how enumerated types and structs can be used to create your own custom data types.

Here is an example of a struct used to hold a date:

```C++
struct DateStruct{
	int year{};
	int month{};
	int day{};
}
```



Enumerated types and data-only structs (structs that only contain variables) represent the traditional non-object-oriented programming world, as they can only hold data. We can create and initialize this struct as follows:

```C++
DateStruct today{2021, 8, 11}; //using uniform initialization
```

**Classes**

In the world of object-oriented programming, we often want our types to not only hold data, but provide functions that work with the data as well. In C++, this is typically done via the **class** keyword. The class keyword defines a new user-defined type called a class.

In C++, classes and structs are essentially the same.

```C++
struct DateStruct
{
    int year {};
    int month {};
    int day {};
};
 
class DateClass
{
public:
    int m_year {};
    int m_month {};
    int m_day {};
};
```

Note that the only significant difference is the *public:* keyword in the class. We will discuss the function of this keyword in the next lesson.

**Note**: Just like a struct declaration, a class declaration does not allocate any memory. It only defines what the class looks like.

**Member Functions**

In addition to holding data, classes (and structs) can also contain functions! Functions defined inside of a class are called **member functions** (or sometimes **methods**). Member functions can be defined inside or outside of the class definition. We’ll define them inside the class for now (for simplicity), and show how to define them outside the class later.

By convention, class names should begin with an upper-case letter.

With normal non-member functions, a function can’t call a function that’s defined “below” it (without a forward declaration):

```C++
void x()
{
// You can't call y() from here unless the compiler has already seen a forward declaration for y()
}
 
void y()
{
}
```

With member functions, this limitation doesn’t apply:

```C++
class foo
{
public:
     void x() { y(); } // okay to call y() here, even though y() isn't defined until later in this class
     void y() { };
};
```

**Member types**

In addition to member variables and member functions, `class`es can have **member types** or **nested types** (including type aliases). In the following example, we’re creating a calculator where we can swiftly change the type of number it’s using if we ever need to.

```C++
#include <iostream>
#include <vector>
 
class Calculator
{
public:
    using number_type = int; // this is a nested type alias
 
    std::vector<number_type> m_resultHistory{};
 
    number_type add(number_type a, number_type b)
    {
        auto result{ a + b };
 
        m_resultHistory.push_back(result);
 
        return result;
    }
};
 
int main()
{
    Calculator calculator;
 
    std::cout << calculator.add(3, 4) << '\n'; // 7
    std::cout << calculator.add(99, 24) << '\n'; // 123
 
    for (Calculator::number_type result : calculator.m_resultHistory)
    {
        std::cout << result << '\n';
    }
 
    return 0;
}
```

In such a context, the class name effectively acts like a namespace for the nested type. From inside the class, we only need reference `number_type`. From outside the class, we can access the type via `Calculator::number_type`.

**Conclusion**: The class keyword lets us create a custom type in C++ that can contain both member variables and member functions. Classes form the basis for Object-oriented programming, and we’ll spend the rest of this chapter and many of the future chapters exploring all they have to offer!

The public keyword, along with the following colon, is called an access specifier. **Access specifiers** determine who has access to the members that follow the specifier. Each of the members “acquires” the access level of the previous access specifier (or, if none is provided, the default access specifier).

C++ provides 3 different access specifier keywords: public, private, and protected. Public and private are used to make the members that follow them public members or private members respectively. The third access specifier, protected, works much like private does. We will discuss the difference between the private and protected access specifier when we cover inheritance.

In general, member variables are usually made private, and member functions are usually made public. We’ll take a closer look at why in the next lesson.

**Rule**: Make member variables private, and member functions public, unless you have a good reason not to.

The group of public members of a class are often referred to as a **public interface**. Because only public members can be accessed from outside of the class, the public interface defines how programs using the class will interact with the class. 

Some programmers prefer to list private members first, because the public members typically use the private ones, so it makes sense to define the private ones first. However, a good counterargument is that users of the class don’t care about the private members, so the public ones should come first. Either way is fine.

**Access controls work on a per-class basis**

Consider the following program:

```C++
#include <iostream>
 
class DateClass // members are private by default
{
	int m_month {}; // private by default, can only be accessed by other members
	int m_day {}; // private by default, can only be accessed by other members
	int m_year {}; // private by default, can only be accessed by other members
 
public:
	void setDate(int month, int day, int year)
	{
		m_month = month;
		m_day = day;
		m_year = year;
	}
 
	void print()
	{
		std::cout << m_month << '/' << m_day << '/' << m_year;
	}
 
	// Note the addition of this function
	void copyFrom(const DateClass& d)
	{
		// Note that we can access the private members of d directly
		m_month = d.m_month;
		m_day = d.m_day;
		m_year = d.m_year;
	}
};
 
int main()
{
	DateClass date;
	date.setDate(10, 14, 2020); // okay, because setDate() is public
	
	DateClass copy {};
	copy.copyFrom(date); // okay, because copyFrom() is public
	copy.print();
	std::cout << '\n';
 
	return 0;
}
```

One nuance of C++ that is often missed or misunderstood is that access control works on a per-class basis, not a per-object basis. This means that when a function has access to the private members of a class, it can access the private members of *any* object of that class type that it can see.

**Why make member variables private?**

In the previous lesson, we mentioned that class member variables are typically made private. Developers who are learning about object-oriented programming often have a hard time understanding why you’d want to do this.

This separation of interface and implementation is extremely useful because it allows us to use objects without understanding how they work. This vastly reduces the complexity of using these objects, and increases the number of objects we’re capable of interacting with. For similar reasons, the separation of implementation and interface is useful in programming.

**Encapsulation**

In object-oriented programming, **Encapsulation** (also called **information hiding**) is the process of keeping the details about how an object is implemented hidden away from users of the object. Instead, users of the object access the object through a public interface. In this way, users are able to use the object without having to understand how it is implemented.

In C++, we implement encapsulation via access specifiers. Typically, all member variables of the class are made private (hiding the implementation details), and most member functions are made public (exposing an interface for the user). Although requiring users of the class to use the public interface may seem more burdensome than providing public access to the member variables directly, doing so actually provides a large number of useful benefits that help encourage class re-usability and maintainability.

Note: The word encapsulation is also sometimes used to refer to the packaging of data and functions that work on that data together. We prefer to just call that object-oriented programming.

**Benefit: encapsulated classes are easier to use and reduce the complexity of your programs**

With a fully encapsulated class, you only need to know what member functions are publicly available to use the class, what arguments they take, and what values they return. It doesn’t matter how the class was implemented internally. For example, a class holding a list of names could have been implemented using a dynamic array of C-style strings, std::array, std::vector, std::map, std::list, or one of many other data structures. In order to use the class, you don’t need to know (or care) which. This dramatically reduces the complexity of your programs, and also reduces mistakes. More than any other reason, this is the key advantage of encapsulation.

All of the classes in the C++ standard library are encapsulated. Imagine how much more complicated C++ would be if you had to understand how std::string, std::vector, or std::cout were implemented in order to use them!

**Benefit: encapsulated classes help protect your data and prevent misuse**

Global variables are dangerous because you don’t have strict control over who has access to the global variable, or how they use it. Classes with public members suffer from the same problem, just on a smaller scale.

**Benefit: encapsulated classes are easier to change**

Encapsulation gives us the ability to change how classes are implemented without breaking all of the programs that use them.

**Benefit: encapsulated classes are easier to debug**

And finally, encapsulation helps you debug the program when something goes wrong. Often when a program does not work correctly, it is because one of our member variables has an incorrect value. If everyone is able to access the variable directly, tracking down which piece of code modified the variable can be difficult (it could be any of them, and you’ll need to breakpoint them all to figure out which). However, if everybody has to call the same public function to modify a value, then you can simply breakpoint that function and watch as each caller changes the value until you see where it goes wrong.

**Access functions**

Access functions typically come in two flavors: getters and setters. **Getters** (also sometimes called **accessors**) are functions that return the value of a private member variable. **Setters** (also sometimes called **mutators**) are functions that set the value of a private member variable.

**Access functions concerns**

There is a fair bit of discussion around in which cases access functions should be used or avoided. Although they don’t violate encapsulation, some developers would argue that use of access functions violates good OOP class design (a topic that could easily fill an entire book).

For now, we’ll recommend a pragmatic approach. As you create your classes, consider the following:

- If nobody outside your class needs to access a member, don’t provide access functions for that member.
- If someone outside your class needs to access a member, think about whether you can expose a behavior or action instead (e.g. rather than a setAlive(bool) setter, implement a kill() function instead).
- If you can’t, consider whether you can provide only a getter.

Constructors

A **constructor** is a special kind of class member function that is automatically called when an object of that class is created. Constructors are typically used to initialize member variables of the class to appropriate default or user-provided values, or to do any setup steps necessary for the class to be used (e.g. open a file or database).

After a constructor executes, the object should be in a well-defined, usable state.

Unlike normal member functions, constructors have specific rules for how they must be named:

1. Constructors must have the same name as the class (with the same capitalization)
2. Constructors have no return type (not even void)

**Default constructors**

A constructor that takes no parameters (or has parameters that all have default values) is called a **default constructor**. The default constructor is called if no user-provided initialization values are provided.

**Copy initialization using equals with classes**

Much like with fundamental variables, it’s also possible to initialize classes using copy initialization:

```C++
Fraction six = Fraction{ 6 }; // Copy initialize a Fraction, will call Fraction(6, 1)
Fraction seven = 7; // Copy initialize a Fraction.  The compiler will try to find a way to convert 7 to a Fraction, which will invoke the Fraction(7, 1) constructor.
```

However, we recommend you avoid this form of initialization with classes, as it may be less efficient. Although direct initialization, uniform initialization, and copy initialization all work identically with fundamental types, copy-initialization does not work the same with classes (though the end-result is often the same). We’ll explore the differences in more detail in a future chapter.

**An implicitly generated default constructor**

If your class has no constructors, C++ will automatically generate a public default constructor for you. This is sometimes called an **implicit constructor** (or implicitly generated constructor).

If your class has any other constructors, the implicitly generated constructor will not be provided.

You can use the default keyword to tell the compiler to create a default constructor for us anyway:

```C++
class Date{private:    int m_year{ 1900 };    int m_month{ 1 };    int m_day{ 1 }; public:    // Tell the compiler to create a default constructor, even if    // there are other user-provided constructors.    Date() = default;     Date(int year, int month, int day) // normal non-default constructor    {        m_year = year;        m_month = month;        m_day = day;    }}; int main(){    Date date{}; // date is initialized to Jan 1st, 1900    Date today{ 2020, 10, 14 }; // today is initialized to Oct 14th, 2020     return 0;}
```

Using `= default` is longer than writing a constructor with an empty body, but expresses better what your intentions are (To create a default constructor), and it’s safer. `= default` also works for other special constructors, which we’ll talk about in the future.

**Classes containing classes**

A `class` may contain other classes as member variables. By default, when the outer class is constructed, the member variables will have their default constructors called. This happens before the body of the constructor executes.

```C++
#include <iostream>
 
class A
{
public:
    A() { std::cout << "A\n"; }
};
 
class B
{
private:
    A m_a; // B contains A as a member variable
 
public:
    B() { std::cout << "B\n"; }
};
 
int main()
{
    B b;
    return 0;
}
```

**Constructor notes**

Many new programmers are confused about whether constructors create the objects or not. They do not -- the compiler sets up the memory allocation for the object prior to the constructor call.

Constructors actually serve two purposes. 

- First, constructors determine who is allowed to create an object. That is, an object of a class can only be created if a matching constructor can be found.

- Second, constructors can be used to initialize objects. Whether the constructor actually does an initialization is up to the programmer. It’s syntactically valid to have a constructor that does no initialization at all (the constructor still serves the purpose of allowing the object to be created, as per the above).

However, much like it is a best practice to initialize all local variables, it’s also a best practice to initialize all member variables on creation of the object. This can be done via a constructor or via non-static member initialization.

Finally, constructors are only intended to be used for initialization when the object is created. You should not try to call a constructor to re-initialize an existing object. While it may compile, the results will not be what you intended (instead, the compiler will create a temporary object and then discard it).

### 12.6 Constructor Member Initializer Lists

In the previous lesson, for simplicity, we initialized our class member data in the constructor using the assignment operator. For example:

```C++
class Something
{
private:
    int m_value1 {};
    double m_value2 {};
    char m_value3 {};
 
public:
    Something()
    {
        // These are all assignments, not initializations
        m_value1 = 1;
        m_value2 = 2.2;
        m_value3 = 'c';
    }
};
```

When the class’s constructor is executed, m_value1, m_value2, and m_value3 are created. Then the body of the constructor is run, where the member data variables are assigned values. This is similar to the flow of the following code in non-object-oriented C++:

```C++
int m_value1 {};
double m_value2 {};
char m_value3 {};
 
m_value1 = 1;
m_value2 = 2.2;
m_value3 = 'c';
```

While this is valid within the syntax of the C++ language, it does not exhibit good style (and may be less efficient than initialization).

However, as you have learned in previous lessons, some types of data (e.g. const and reference variables) must be initialized on the line they are declared. Consider the following example:

```C++
class Something
{
private:
    const int m_value;
 
public:
    Something()
    {
        m_value = 1; // error: const vars can not be assigned to
    } 
};
```

In previous lessons, you learned that you could initialize variables in three ways: copy, direct, and via uniform initialization.

```C++
int value1 = 1; // copy initializationdouble value2(2.2); // direct initializationchar value3 {'c'}; // uniform initialization
```

Using an initialization list is almost identical to doing direct initialization or uniform initialization.

```C++
class Something
{
private:
    int m_value1 {};
    double m_value2 {};
    char m_value3 {};
 
public:
    Something() : m_value1{ 1 }, m_value2{ 2.2 }, m_value3{ 'c' } // Initialize our member variables
    {
    // No need for assignment here
    }
 
    void print()
    {
         std::cout << "Something(" << m_value1 << ", " << m_value2 << ", " << m_value3 << ")\n";
    }
};
 
int main()
{
    Something something{};
    something.print();
    return 0;
}
```

The member initializer list is inserted after the constructor parameters. It begins with a colon (:), and then lists each variable to initialize along with the value for that variable separated by a comma.

Note that we no longer need to do the assignments in the constructor body, since the initializer list replaces that functionality. Also note that the initializer list does not end in a semicolon.

**Rule**: Use member initializer lists to initialize your class member variables instead of assignment.

**Initializer list order**: Perhaps surprisingly, variables in the initializer list are not initialized in the order that they are specified in the initializer list. Instead, they are initialized in the order in which they are declared in the class.

For best results, the following recommendations should be observed:
\(1) Don’t initialize member variables in such a way that they are dependent upon other member variables being initialized first (in other words, ensure your member variables will properly initialize even if the initialization ordering is different).
\(2) Initialize variables in the initializer list in the same order in which they are declared in your class. This isn’t strictly required so long as the prior recommendation has been followed, but your compiler may give you a warning if you don’t do so and you have all warnings turned on.

**Summary**

Member initializer lists allow us to initialize our members rather than assign values to them. This is the only way to initialize members that require values upon initialization, such as const or reference members, and it can be more performant than assigning values in the body of the constructor. Member initializer lists work both with fundamental types and members that are classes themselves. Prior to C++11, you can only zero an array member via a member initialization list. However, since C++11, you can fully initialize a member array using uniform initializatio

### 12.7 Non-static Member Initialization

When writing a class that has multiple constructors (which is most of them), having to specify default values for all members in each constructor results in redundant code. If you update the default value for a member, you need to touch each constructor.

It’s possible to give normal class member variables (those that don’t use the static keyword) a default initialization value directly:

```C++
#include <iostream>
 
class Rectangle
{
private:
    double m_length{ 1.0 }; // m_length has a default value of 1.0
    double m_width{ 1.0 }; // m_width has a default value of 1.0
 
public:
    void print()
    {
        std::cout << "length: " << m_length << ", width: " << m_width << '\n';
    }
};
 
int main()
{
    Rectangle x{}; // x.m_length = 1.0, x.m_width = 1.0
    x.print();
 
    return 0;
}
```

Non-static member initialization (also called in-class member initializers) provides default values for your member variables that your constructors will use if the constructors do not provide initialization values for the members themselves (via the member initialization list).

However, note that constructors still determine what kind of objects may be created.

```C++
#include <iostream>
 
class Rectangle
{
private:
    double m_length{ 1.0 };
    double m_width{ 1.0 };
 
public:
 
    // note: No default constructor provided in this example
 
    Rectangle(double length, double width)
        : m_length{ length },
          m_width{ width }
    {
        // m_length and m_width are initialized by the constructor (the default values aren't used)
    }
 
    void print()
    {
        std::cout << "length: " << m_length << ", width: " << m_width << '\n';
    }
 
};
 
int main()
{
    Rectangle x{}; // will not compile, no default constructor exists, even though members have default initialization values
 
    return 0;
}
```

Even though we’ve provided default values for all members, no default constructor has been provided, so we are unable to create Rectangle objects with no arguments.

If a default initialization value is provided and the constructor initializes the member via the member initializer list, the member initializer list will take precedence. The following example shows this:

```C++
#include <iostream>
 
class Rectangle
{
private:
    double m_length{ 1.0 };
    double m_width{ 1.0 };
 
public:
 
    Rectangle(double length, double width)
        : m_length{ length },
          m_width{ width }
    {
        // m_length and m_width are initialized by the constructor (the default values aren't used)
    }
 
    Rectangle(double length)
        : m_length{ length }
    {
        // m_length is initialized by the constructor.
        // m_width's default value (1.0) is used.
    }
 
    void print()
    {
        std::cout << "length: " << m_length << ", width: " << m_width << '\n';
    }
 
};
 
int main()
{
    Rectangle x{ 2.0, 3.0 };
    x.print();
 
    Rectangle y{ 4.0 };
    y.print();
 
    return 0;
}
```

Note that initializing members using non-static member initialization requires using either an equals sign, or a brace (uniform) initializer -- the direct initialization form doesn’t work here.

### 12.8 Overlapping and delegating Constructors

When you instantiate a new object, the object’s constructor is called implicitly. It’s not uncommon to have a class with multiple constructors that have overlapping functionality. Consider the following class:

```C++
class Foo
{
public:
    Foo()
    {
        // code to do A
    }
 
    Foo(int value)
    {
        // code to do A
        // code to do B
    }
};
```

This class has two constructors: a default constructor, and a constructor that takes an integer. Because the “code to do A” portion of the constructor is required by both constructors, the code is duplicated in each constructor.

As you’ve (hopefully) learned by now, having duplicate code is something to be avoided as much as possible, so let’s take a look at some ways to address this.

The obvious solution would be to have the Foo(int) constructor call the Foo() constructor to do the A portion. **But it doesn't work.**

**Delegating constructors**

Constructors are allowed to call other constructors from the same class. This process is called **delegating constructors** (or **constructor chaining**).

To have one constructor call another, simply call the constructor in the member initializer list. This is one case where calling another constructor directly is acceptable. Applied to our example above:

```C++
class Foo
{
private:
 
public:
    Foo()
    {
        // code to do A
    }
 
    Foo(int value): Foo{} // use Foo() default constructor to do A
    {
        // code to do B
    }
 
};
```

Here’s another example of using delegating constructors to reduce redundant code:

```C++
#include <string>
#include <iostream>
 
class Employee
{
private:
    int m_id{};
    std::string m_name{};
 
public:
    Employee(int id=0, const std::string &name=""):
        m_id{ id }, m_name{ name }
    {
        std::cout << "Employee " << m_name << " created.\n";
    }
 
    // Use a delegating constructor to minimize redundant code
    Employee(const std::string &name) : Employee{ 0, name }
    { }
};
```



**A few additional notes about delegating constructors**.

- First, a constructor that delegates to another constructor is not allowed to do any member initialization itself. So your constructors can delegate or initialize, but not both.
- Second, it’s possible for one constructor to delegate to another constructor, which delegates back to the first constructor. This forms an infinite loop, and will cause your program to run out of stack space and crash. You can avoid this by ensuring all of your constructors resolve to a non-delegating constructor.

**Best practice**: If you have multiple constructors that have the same functionality, use delegating constructors to avoid duplicate code.

**Using a separate function**

Relatedly, you may find yourself in the situation where you want to write a member function to re-initialize a class back to default values. Because you probably already have a constructor that does this, you may be tempted to try to call the constructor from your member function. However, trying to call a constructor directly will generally result in unexpected behavior as we have shown above. Many developers simply copy the code from the constructor into the initialization function, which would work, but lead to duplicate code. The best solution in this case is to move the code from the constructor to your new function, and have the constructor call your function to do the work of “initializing” the data:

```C++
class Foo
{
public:
    Foo()
    {
        init();
    }
 
    Foo(int value)
    {
        init();
        // do something with value
    }
 
    void init()
    {
        // code to "initialize" Foo
    }
};
```

Constructors *are* allowed to call non-constructor functions in the class. Just be careful that any members the non-constructor function uses have already been initialized. Although you may be tempted to copy code from the first constructor into the second constructor, having duplicate code makes your class harder to understand and more burdensome to maintain.

We say “initialize”, but it’s not real initialization. By the time the constructor calls `init()`, the members already exist and have been default initialized or are uninitialized. The `init` function can only assign values to the members. There are some types that cannot be instantiated without arguments, because they don’t have a default constructor. If any of the class members has such a type, the `init` function doesn’t work and the constructors have to initialize those members themselves.

It is fairly common to include an init() function that initializes member variables to their default values, and then have each constructor call that init() function before doing its parameter-specific tasks. This minimizes code duplication and allows you to explicitly call init() from wherever you like.

One small caveat: be careful when using init() functions and dynamically allocated memory. Because init() functions can be called by anyone at any time, dynamically allocated memory may or may not have already been allocated when init() is called. Be careful to handle this situation appropriately -- it can be slightly confusing, since a non-null pointer could be either dynamically allocated memory or an uninitialized pointer!

### 12.9 Destructors

A **destructor** is another special kind of class member function that is executed when an object of that class is destroyed. Whereas constructors are designed to initialize a class, destructors are designed to help clean up.

When an object goes out of scope normally, or a dynamically allocated object is explicitly deleted using the delete keyword, the class destructor is automatically called (if it exists) to do any necessary clean up before the object is removed from memory. For simple classes (those that just initialize the values of normal member variables), a destructor is not needed because C++ will automatically clean up the memory for you.

However, if your class object is holding any resources (e.g. dynamic memory, or a file or database handle), or if you need to do any kind of maintenance before the object is destroyed, the destructor is the perfect place to do so, as it is typically the last thing to happen before the object is destroyed.

```C++
#include <iostream>
#include <cassert>
#include <cstddef>
 
class IntArray
{
private:
	int *m_array{};
	int m_length{};
 
public:
	IntArray(int length) // constructor
	{
		assert(length > 0);
 
		m_array = new int[static_cast<std::size_t>(length)]{};
		m_length = length;
	}
 
	~IntArray() // destructor
	{
		// Dynamically delete the array we allocated earlier
		delete[] m_array;
	}
 
	void setValue(int index, int value) { m_array[index] = value; }
	int getValue(int index) { return m_array[index]; }
 
	int getLength() { return m_length; }
};
 
int main()
{
	IntArray ar ( 10 ); // allocate 10 integers
	for (int count{ 0 }; count < ar.getLength(); ++count)
		ar.setValue(count, count+1);
 
	std::cout << "The value of element 5 is: " << ar.getValue(5) << '\n';
 
	return 0;
} // ar is destroyed here, so the ~IntArray() destructor function is called here
```

On the first line, we instantiate a new IntArray class object called ar, and pass in a length of 10. This calls the constructor, which dynamically allocates memory for the array member. We must use dynamic allocation here because we do not know at compile time what the length of the array is (the caller decides that).

At the end of main(), ar goes out of scope. This causes the ~IntArray() destructor to be called, which deletes the array that we allocated in the constructor!

**Destructor naming**

Like constructors, destructors have specific naming rules:

​	(1) The destructor must have the same name as the class, preceded by a tilde (~).

​	(2) The destructor can not take arguments.

​	(3)The destructor has no return type.

Note that rule 2 implies that only one destructor may exist per class, as there is no way to overload destructors since they can not be differentiated from each other based on arguments.

Generally you should not call a destructor explicitly (as it will be called automatically when the object is destroyed), since there are rarely cases where you’d want to clean up an object more than once. However, destructors may safely call other member functions since the object isn’t destroyed until after the destructor executes.

Global variables are constructed before main() and destroyed after main().

**RAII**

RAII (Resource Acquisition Is Initialization) is a programming technique whereby resource use is tied to the lifetime of objects with automatic duration (e.g. non-dynamically allocated objects). In C++, RAII is implemented via classes with constructors and destructors. A resource (such as memory, a file or database handle, etc…) is typically acquired in the object’s constructor (though it can be acquired after the object is created if that makes sense). That resource can then be used while the object is alive. The resource is released in the destructor, when the object is destroyed. The primary advantage of RAII is that it helps prevent resource leaks (e.g. memory not being deallocated) as all resource-holding objects are cleaned up automatically.

The IntArray class at the top of this lesson is an example of a class that implements RAII -- allocation in the constructor, deallocation in the destructor. std::string and std::vector are examples of classes in the standard library that follow RAII -- dynamic memory is acquired on initialization, and cleaned up automatically on destruction.

**A warning about the exit() function**: Note that if you use the exit() function, your program will terminate and no destructors will be called. Be wary if you’re relying on your destructors to do necessary cleanup work (e.g. write something to a log file or database before exiting).

**Summary**: As you can see, when constructors and destructors are used together, your classes can initialize and clean up after themselves without the programmer having to do any special work! This reduces the probability of making an error, and makes classes easier to use.

### 12.10 The Hidden "this" pointer

One of the questions about classes that new object-oriented programmers often ask is, “When a member function is called, how does C++ keep track of which object it was called on?”. The answer is that C++ utilizes a hidden pointer named “this”! Let’s take a look at “this” in more detail.

```C++
class Simple
{
private:
    int m_id;
 
public:
    Simple(int id)
        : m_id{ id }
    {
    }
 
    void setID(int id) { m_id = id; }
    int getID() { return m_id; }
};

int main()
{
    Simple simple{1};
    simple.setID(2);
    std::cout << simple.getID() << '\n';
 
    return 0;
}
```

Somehow, when we call `simple.setID(2);`, C++ knows that function setID() should operate on object simple, and that m_id actually refers to simple.m_id. Let’s examine the mechanics behind how this works.

**The hidden \*this pointer**

Take a look at the following line of code from the example above:

`simple.setID(2);`

Although the call to function setID() looks like it only has one argument, it actually has two! When compiled, the compiler converts `simple.setID(2);` into the following:

```C++
setID(&simple, 2); // note that simple has been changed from an object prefix to a function argument!
```

Note that this is now just a standard function call, and the object simple (which was formerly an object prefix) is now passed by address as an argument to the function.

But that’s only half of the answer. Since the function call now has an added argument, the member function definition needs to be modified to accept (and use) this argument as a parameter. Consequently, the following member function:

```C++
void setID(int id) { m_id = id; }
```

is converted by the compiler into:

```C++
void setID(Simple* const this, int id) { this->m_id = id; }
```

When the compiler compiles a normal member function, it implicitly adds a new parameter to the function named “this”. The **this pointer** is a hidden const pointer that holds the address of the object the member function was called on.

There’s just one more detail to take care of. Inside the member function, any class members (functions and variables) also need to be updated so they refer to the object the member function was called on. This is easily done by adding a “this->” prefix to each of them. Thus, in the body of function setID(), `m_id` (which is a class member variable) has been converted to `this->m_id`. Thus, when “this” points to the address of simple, this->m_id will resolve to simple.m_id.

\(1) When we call `simple.setID(2)`, the compiler actually calls setID(&simple, 2).
\(2) Inside setID(), the “this” pointer holds the address of object simple.
\(3) Any member variables inside setID() are prefixed with “this->”. So when we say `m_id = id`, the compiler is actually executing `this->m_id = id`, which in this case updates simple.m_id to id.

The good news is that all of this happens automatically, and it doesn’t really matter whether you remember how it works or not. All you need to remember is that all normal member functions have a “this” pointer that refers to the object the function was called on.

**“this” always points to the object being operated on**

New programmers are sometimes confused about how many “this” pointers exist. Each member function has a “this” pointer parameter that is set to the address of the object being operated on. 

Because “this” is just a function parameter, it doesn’t add any memory usage to your class (just to the member function call, since that parameter needs to be passed to the function and stored in memory).

**Explicitly referencing “this”**

Some developers prefer to explicitly add this-> to all class members. We recommend that you avoid doing so, as it tends to make your code less readable for little benefit. Using the m_ prefix is a more readable way to differentiate member variables from non-member (local) variables.

**Summary**: The “this” pointer is a hidden parameter implicitly added to any non-static member function. Most of the time, you will not need to access it directly, but you can if needed. It’s worth noting that “this” is a const pointer -- you can change the value of the underlying object it points to, but you can not make it point to something else!

By having functions that would otherwise return void return *this instead, you can make those functions chainable. This is most often used when overloading operators for your classes 

### 12.11 Class Code and header files

All of the classes that we have written so far have been simple enough that we have been able to implement the member functions directly inside the class definition itself. 

However, as classes get longer and more complicated, having all the member function definitions inside the class can make the class harder to manage and work with. Using an already-written class only requires understanding its public interface (the public member functions), not how the class works underneath the hood. The member function implementation details just get in the way.

Fortunately, C++ provides a way to separate the “declaration” portion of the class from the “implementation” portion. This is done by defining the class member functions outside of the class definition. To do so, simply define the member functions of the class as if they were normal functions, but prefix the class name to the function using the scope resolution operator (::) (same as for a namespace).

**Libraries**

Separating the class definition and class implementation is very common for libraries that you can use to extend your program. Throughout your programs, you’ve #included headers that belong to the standard library, such as iostream, string, vector, array, and other. Notice that you haven’t needed to add iostream.cpp, string.cpp, vector.cpp, or array.cpp into your projects. Your program needs the declarations from the header files in order for the compiler to validate you’re writing programs that are syntactically correct. However, the implementations for the classes that belong to the C++ standard library are contained in a precompiled file that is linked in at the link stage. You never see the code.

Outside of some open source software (where both .h and .cpp files are provided), most 3rd party libraries provide only header files, along with a precompiled library file. There are several reasons for this: 1) It’s faster to link a precompiled library than to recompile it every time you need it, 2) a single copy of a precompiled library can be shared by many applications, whereas compiled code gets compiled into every executable that uses it (inflating file sizes), and 3) intellectual property reasons (you don’t want people stealing your code).

Having your own files separated into declaration (header) and implementation (code file) is not only good form, it also makes creating your own custom libraries easier. Creating your own libraries is beyond the scope of these tutorials, but separating your declaration and implementation is a prerequisite to doing so.





### 12.13 Static Member Variables

In the previous lessons, you learned that static variables keep their values and are not destroyed even after they go out of scope. 

The static keyword has another meaning when applied to global variables -- it gives them internal linkage (which restricts them from being seen/used outside of the file they are defined in). Because global variables are typically avoided, the static keyword is not often used in this capacity.

Member variables of a class can be made static by using the static keyword. Unlike normal member variables, static member variables are shared by all objects of the class. Consider the following program, similar to the above:

```C++
class Something
{
public:
    static int s_value;
};
 
int Something::s_value{ 1 };
 
int main()
{
    Something first;
    Something second;
 
    first.s_value = 2;
 
    std::cout << first.s_value << '\n';
    std::cout << second.s_value << '\n';
    return 0;
}
```

Because s_value is a static member variable, s_value is shared between all objects of the class. Consequently, first.s_value is the same variable as second.s_value. The above program shows that the value we set using first can be accessed using second!

**Static members are not associated with class objects**, it can be accessed directly using the class name and the scope resolution operator.

**Defining and initializing static member variables**

When we declare a static member variable inside a class, we’re telling the compiler about the existence of a static member variable, but not actually defining it (much like a forward declaration). Because static member variables are not part of the individual class objects (they are treated similarly to global variables, and get initialized when the program starts), you must explicitly define the static member outside of the class, in the global scope.

In the example above, we do so via this line:

```C++
int Something::s_value{ 1 }; // defines the static member variable
```

This line serves two purposes: it instantiates the static member variable (just like a global variable), and optionally initializes it. In this case, we’re providing the initialization value 1. If no initializer is provided, C++ initializes the value to 0.

Note that this static member definition is not subject to access controls: you can define and initialize the value even if it’s declared as private (or protected) in the class.

Note that this static member definition is not subject to access controls: you can define and initialize the value even if it’s declared as private (or protected) in the class.

If the class is defined in a .h file, the static member definition is usually placed in the associated code file for the class (e.g. Something.cpp). If the class is defined in a .cpp file, the static member definition is usually placed directly underneath the class. Do not put the static member definition in a header file (much like a global variable, if that header file gets included more than once, you’ll end up with multiple definitions, which will cause a linker error).

**Inline initialization of static member variables**

There are a few shortcuts to the above. First, when the static member is a const integral type (which includes char and bool) or a const enum, the static member can be initialized inside the class definition.

```C++
class Whatever
{
public:
    static const int s_value{ 4 }; // a static const int can be declared and initialized directly
};
```

Second, static constexpr members can be initialized inside the class definition.

```C++
#include <array>
 
class Whatever
{
public:
    static constexpr double s_value{ 2.2 }; // ok
    static constexpr std::array<int, 3> s_array{ 1, 2, 3 }; // this even works for classes that support constexpr initialization
};
```

Why use static variables inside classes? One great example is to assign a unique ID to every instance of the class.

Static member variables can also be useful when the class needs to utilize an internal lookup table (e.g. an array used to store a set of pre-calculated values). By making the lookup table static, only one copy exists for all objects, rather than making a copy for each object instantiated. This can save substantial amounts of memory.

### 12.14 Static Member Functions

In the previous lesson, you learned that static member variables are member variables that belong to the class rather than objects of the class. If the static member variables are public, we can access them directly using the class name and the scope resolution operator. But what if the static member variables are private?

Normally we access private members through public member functions. While we could create a normal public member function to access s_value, we’d then need to instantiate an object of the class type to use the function! We can do better. It turns out that we can also make functions static.

Like static member variables, static member functions are not attached to any particular object. 

Because static member functions are not attached to a particular object, they can be called directly by using the class name and the scope resolution operator. Like static member variables, they can also be called through objects of the class type, though this is not recommended.

**Static member functions have no *this pointer**

Static member functions have two interesting quirks worth noting. First, because static member functions are not attached to an object, they have no *this* pointer! This makes sense when you think about it -- the *this* pointer always points to the object that the member function is working on. Static member functions do not work on an object, so the *this* pointer is not needed.

Second, static member functions can directly access other static members (variables or functions), but not non-static members. This is because non-static members must belong to a class object, and static member functions have no class object to work with!

### 12.15 Friend Functions and Classes

For much of this chapter, we’ve been preaching the virtues of keeping your data private. However, you may occasionally find situations where you will find you have classes and functions outside of those classes that need to work very closely together. For example, you might have a class that stores data, and a function (or another class) that displays the data on the screen. Although the storage class and display code have been separated for easier maintenance, the display code is really intimately tied to the details of the storage class. Consequently, there isn’t much to gain by hiding the storage classes details from the display code.

In situations like this, there are two options:
\(1) Have the display code use the publicly exposed functions of the storage class. However, this has several potential downsides. First, these public member functions have to be defined, which takes time, and can clutter up the interface of the storage class. Second, the storage class may have to expose functions for the display code that it doesn’t really want accessible to anybody else. There is no way to say “this function is meant to be used by the display class only”.

\(2) Alternatively, using friend classes and friend functions, you can give your display code access to the private details of the storage class. This lets the display code directly access all the private members and functions of the storage class, while keeping everyone else out! In this lesson, we’ll take a closer look at how this is done.

**Friend functions**

A **friend function** is a function that can access the private members of a class as though it were a member of that class. In all other regards, the friend function is just like a normal function. A friend function may be either a normal function, or a member function of another class. To declare a friend function, simply use the *friend* keyword in front of the prototype of the function you wish to be a friend of the class. It does not matter whether you declare the friend function in the private or public section of the class.

**Friend classes**

It is also possible to make an entire class a friend of another class. This gives all of the members of the friend class access to the private members of the other class. Here is an example:

```C++
#include <iostream>
 
class Storage
{
private:
    int m_nValue;
    double m_dValue;
public:
    Storage(int nValue, double dValue)
    {
        m_nValue = nValue;
        m_dValue = dValue;
    }
 
    // Make the Display class a friend of Storage
    friend class Display;
};
 
class Display
{
private:
    bool m_displayIntFirst;
 
public:
    Display(bool displayIntFirst) { m_displayIntFirst = displayIntFirst; }
 
    void displayItem(const Storage& storage)
    {
        if (m_displayIntFirst)
            std::cout << storage.m_nValue << ' ' << storage.m_dValue << '\n';
        else // display double first
            std::cout << storage.m_dValue << ' ' << storage.m_nValue << '\n';
    }
};
 
int main()
{
    Storage storage(5, 6.7);
    Display display(false);
 
    display.displayItem(storage);
 
    return 0;
}
```

A few additional notes on friend classes. First, even though Display is a friend of Storage, Display has no direct access to the *this pointer of Storage objects. Second, just because Display is a friend of Storage, that does not mean Storage is also a friend of Display. If you want two classes to be friends of each other, both must declare the other as a friend. Finally, if class A is a friend of B, and B is a friend of C, that does not mean A is a friend of C.

Be careful when using friend functions and classes, because it allows the friend function or class to violate encapsulation. If the details of the class change, the details of the friend will also be forced to change. Consequently, limit your use of friend functions and classes to a minimum.

**Friend member functions**

Instead of making an entire class a friend, you can make a single member function a friend. This is done similarly to making a normal function a friend, except using the name of the member function with the className:: prefix included (e.g. Display::displayItem).

### 12.16 Anonymous Objects

In certain cases, we need a variable only temporarily. For example, consider the following situation:

```C++
#include <iostream>
 
int add(int x, int y)
{
    int sum{ x + y };
    return sum;
}
 
int main()
{
    std::cout << add(5, 3) << '\n';
 
    return 0;
}
```

In the `add()` function, note that the sum variable is really only used as a temporary placeholder variable. It doesn’t contribute much -- rather, its only function is to transfer the result of the expression to the return value.

There is actually an easier way to write the add() function using an anonymous object. An **anonymous object** is essentially a value that has no name. Because they have no name, there’s no way to refer to them beyond the point where they are created. Consequently, they have “expression scope”, meaning they are created, evaluated, and destroyed all within a single expression.

Here is the add() function rewritten using an anonymous object:

```C++
#include <iostream>
 
int add(int x, int y)
{
    return x + y; // an anonymous object is created to hold and return the result of x + y
}
 
int main()
{
    std::cout << add(5, 3) << '\n';
 
    return 0;
}
```

When the expression `x + y` is evaluated, the result is placed in an anonymous object. A copy of the anonymous object is then returned to the caller by value, and the anonymous object is destroyed.

This works not only with return values, but also with function parameters.

Note how much cleaner this keeps our code -- we don’t have to litter the code with temporary variables that are only used once.

**Anonymous class objects**

Although our prior examples have been with built-in data types, it is possible to construct anonymous objects of our own class types as well. This is done by creating objects like normal, but omitting the variable name.

```C++
Cents cents{ 5 }; // normal variable
Cents{ 7 }; // anonymous object
```

In the above code, `Cents{ 7 }` will create an anonymous Cents object, initialize it with the value 7, and then destroy it. 

**Summary**

In C++, anonymous objects are primarily used either to pass or return values without having to create lots of temporary variables to do so. Memory allocated dynamically is also done so anonymously (which is why its address must be assigned to a pointer, otherwise we’d have no way to refer to it).

However, it is worth noting that anonymous objects are treated as rvalues (not lvalues, which have an address) -- therefore, all rules about passing and returning rvalues apply.

It is also worth noting that because anonymous objects have expression scope, they can only be used once. If you need to reference a value in multiple expressions, you should use a named variable instead.

Note: Some compilers, such as Visual Studio, will let you set non-const references to anonymous objects. This is non-standard behavior.

### 12.17 Nested Types in Classes 

Consider the following short program:

```C++
#include <iostream>
 
enum class FruitType
{
	apple,
	banana,
	cherry
};
 
class Fruit
{
private:
	FruitType m_type {};
	int m_percentageEaten { 0 };
 
public:
 
 
	Fruit(FruitType type) :
		m_type { type }
	{
	}
 
	FruitType getType() const { return m_type;  }
	int getPercentageEaten() const { return m_percentageEaten;  }
};
 
int main()
{
	Fruit apple { FruitType::apple };
	
	if (apple.getType() == FruitType::apple)
		std::cout << "I am an apple";
	else
		std::cout << "I am not an apple";
	
	return 0;
}
```

There’s nothing wrong with this program. But because enum FruitType is meant to be used in conjunction with the Fruit class, it’s a little weird to have it exist independently from the class itself.

**Nesting types**

Much like functions and data can be members of a class, in C++, types can also be defined (nested) inside of a class. To do this, you simply define the type inside the class, under the appropriate access specifier.

Here’s the same program as above, with FruitType defined inside the class:

```C++
#include <iostream>
 
class Fruit
{
public:
	// Note: we've moved FruitType inside the class, under the public access specifier
	enum FruitType
	{
		apple,
		banana,
		cherry
	};
 
private:
	FruitType m_type {};
	int m_percentageEaten { 0 };
 
public:
 
 
	Fruit(FruitType type) :
		m_type { type }
	{
	}
 
	FruitType getType() const { return m_type;  }
	int getPercentageEaten() const { return m_percentageEaten;  }
};
 
int main()
{
	// Note: we access the FruitType via Fruit now
	Fruit apple { Fruit::apple };
	
	if (apple.getType() == Fruit::apple)
		std::cout << "I am an apple";
	else
		std::cout << "I am not an apple";
	
	return 0;
}
```

First, note that FruitType is now defined inside the class. Second, note that we’ve defined it under the public access specifier, so the type definition can be accessed from outside the class.

Classes essentially act as a namespace for any nested types, much as enum classes do. In the prior example, because we used an enum class, we had to qualify our enumerators with the FruitType:: scope qualifier. In this example, because FruitType is a normal enum that is part of the class, we access our enumerators using the Fruit:: scope qualifier.

Note that because enum classes also act like namespaces, if we’d nested FruitType inside Fruit as an enum class instead of an enum, we’d access the enumeration via a Fruit::FruitType:: scope qualifier. This double-scoping is unnecessary, so we’ve used a normal enum.

**Other types can be nested too**

Although enumerations are probably the most common type that is nested inside a class, C++ will let you define other types within a class, such as typedefs, type aliases, and even other classes!

Like any normal member of a class, nested classes have the same access to members of the enclosing class that the enclosing class does. However, the nested class does not have any special access to the “this” pointer of the enclosing class.

One other limitation of nested types -- they can’t be forward declared. However, this is rarely a problem in practice since the entire class definition (including the nested type) can generally be #included where needed.

Defining nested classes isn’t very common, but the C++ standard library does do so in some cases, such as with iterator classes.

### 12.18 Timing Your Code

When writing your code, sometimes you’ll run across cases where you’re not sure whether one method or another will be more performant. So how do you tell?

One ease way is to time your code to see how long it takes to run.  C++11 comes with some functionality in the chrono library to do just that. However, using the chrono library is a bit arcane. The good news is that we can easily encapsulate all the timing functionality we need into a class that we can then use in our own programs. 

Here’s the class:

```C++
#include <chrono> // for std::chrono functions

class Timer
{
private:
	// Type aliases to make accessing nested type easier
	using clock_type = std::chrono::steady_clock;
	using second_type = std::chrono::duration<double, std::ratio<1> >;

	std::chrono::time_point<clock_type> m_beg;

public:
	Timer() : m_beg { clock_type::now() }
	{
	}

	void reset()
	{
		m_beg = clock_type::now();
	}

	double elapsed() const
	{
		return std::chrono::duration_cast<second_type>(clock_type::now() - m_beg).count();
	}
};
```

That’s it! To use it, we instantiate a Timer object at the top of our main function (or wherever we want to start timing), and then call the elapsed() member function whenever we want to know how long the program took to run to that point.

```C++
int main()
{
    Timer t;

    // Code to time goes here

    std::cout << "Time elapsed: " << t.elapsed() << " seconds\n";

    return 0;
}
```

Now, let’s use this in an actual example where we sort an array of 10000 elements. First, let’s use the selection sort algorithm we developed in a previous chapter:

```C++
#include <array>
#include <chrono> // for std::chrono functions
#include <cstddef> // for std::size_t
#include <iostream>
#include <numeric> // for std::iota

const int g_arrayElements = 10000;

class Timer
{
private:
  // Type aliases to make accessing nested type easier
  using clock_type = std::chrono::steady_clock;
  using second_type = std::chrono::duration<double, std::ratio<1>>;

  std::chrono::time_point<clock_type> m_beg;

public:
  Timer() : m_beg { clock_type::now() }
  {
  }

  void reset()
  {
    m_beg = clock_type::now();
  }

  double elapsed() const
  {
    return std::chrono::duration_cast<second_type>(clock_type::now() - m_beg).count();
  }
};

void sortArray(std::array<int, g_arrayElements>& array)
{

  // Step through each element of the array
  // (except the last one, which will already be sorted by the time we get there)
  for (std::size_t startIndex{ 0 }; startIndex < (g_arrayElements - 1); ++startIndex)
  {
    // smallestIndex is the index of the smallest element we’ve encountered this iteration
    // Start by assuming the smallest element is the first element of this iteration
    std::size_t smallestIndex{ startIndex };

    // Then look for a smaller element in the rest of the array
    for (std::size_t currentIndex{ startIndex + 1 }; currentIndex < g_arrayElements; ++currentIndex)
    {
      // If we've found an element that is smaller than our previously found smallest
      if (array[currentIndex] < array[smallestIndex])
      {
        // then keep track of it
        smallestIndex = currentIndex;
      }
    }

    // smallestIndex is now the smallest element in the remaining array
    // swap our start element with our smallest element (this sorts it into the correct place)
    std::swap(array[startIndex], array[smallestIndex]);
  }
}

int main()
{
  std::array<int, g_arrayElements> array;
  std::iota(array.rbegin(), array.rend(), 1); // fill the array with values 10000 to 1

  Timer t;

  sortArray(array);

  std::cout << "Time taken: " << t.elapsed() << " seconds\n";

  return 0;
}
```

On the author’s machine, three runs produced timings of 0.0507, 0.0506, and 0.0498. So we can say around 0.05 seconds.

Now, let’s do the same test using std::sort from the standard library.

```C++
#include <algorithm> // for std::sort
#include <array>
#include <chrono> // for std::chrono functions
#include <cstddef> // for std::size_t
#include <iostream>
#include <numeric> // for std::iota

const int g_arrayElements = 10000;

class Timer
{
private:
  // Type aliases to make accessing nested type easier
  using clock_type = std::chrono::steady_clock;
  using second_type = std::chrono::duration<double, std::ratio<1>>;

  std::chrono::time_point<clock_type> m_beg;

public:
  Timer() : m_beg { clock_type::now() }
  {
  }

  void reset()
  {
    m_beg = clock_type::now();
  }

  double elapsed() const
  {
    return std::chrono::duration_cast<second_type>(clock_type::now() - m_beg).count();
  }
};

int main()
{
  std::array<int, g_arrayElements> array;
  std::iota(array.rbegin(), array.rend(), 1); // fill the array with values 10000 to 1

  Timer t;

  std::ranges::sort(array); // Since C++20
  // If your compiler isn't C++20-capable
  // std::sort(array.begin(), array.end());

  std::cout << "Time taken: " << t.elapsed() << " seconds\n";

  return 0;
}
```

**A few caveats about timing**

Timing is straightforward, but your results can be significantly impacted by a number of things, and it’s important to be aware of what those things are.

First, make sure you’re using a release build target, not a debug build target. Debug build targets typically turn optimization off, and that optimization can have a significant impact on the results. For example, using a debug build target, running the above std::sort example on the author’s machine took 0.0235 seconds -- 33 times as long!

Second, your timing results will be influenced by other things your system may be doing in the background. For best results, make sure your system isn’t doing anything CPU or memory intensive (e.g. playing a game) or hard drive intensive (e.g. searching for a file or running an antivirus scan).

Then measure at least 3 times. If the results are all similar, take the average. If one or two results are different, run the program a few more times until you get a better sense of which ones are outliers. Note that seemingly innocent things, like web browsers, can temporarily spike your CPU to 100% utilization when the site you have sitting in the background rotates in a new ad banner and has to parse a bunch of javascript. Running multiple times helps identify whether your initial run may have been impacted by such an event.

Third, when doing comparisons between two sets of code, be wary of what may change between runs that could impact timing. Your system may have kicked off an antivirus scan in the background, or maybe you’re streaming music now when you weren’t previously. Randomization can also impact timing. If we’d sorted an array filled with random numbers, the results could have been impacted by the randomization. Randomization can still be used, but ensure you use a fixed seed (e.g. don’t use the system clock) so the randomization is identical each run. Also, make sure you’re not timing waiting for user input, as how long the user takes to input something should not be part of your timing considerations.

Finally, note that results are only valid for your machine’s architecture, OS, compiler, and system specs. You may get different results on other systems that have different strengths and weaknesses.
