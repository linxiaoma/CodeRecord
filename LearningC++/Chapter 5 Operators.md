## Chapter 5 Operators

There two points need to note: Operators precedence and Operators associativity.

The modulus operator can also work with negative operands. `x % y` always returns results with the sign of *x*.

```C++
// using the non-intuitive “exponentiation by squaring” algorithm for efficiency

std::int_fast64_t pow(int base, int exp)
{
    std::int_fast64_t result{ 1 };
    while (exp)
    {
        if (exp & 1)
            result *= base;
        exp >>= 1;
        base *= base;
    }

    return result;
}
```

**Best Practice**: Strongly favor the prefix version of the increment and decrement operators, as they are generally more performant, and you’re less likely to run into strange issues with them.

The ?: operator takes the following form:

`(condition) ? expression1 : expression2;`

**Comparison of Float Point Values**

**Warning**: Avoid using operator== and operator!= with floating point operands.

The most common method of doing floating point equality involves using a function that looks to see if two numbers are *almost* the same. If they are “close enough”, then we call them equal. The value used to represent “close enough” is traditionally called **epsilon**. Epsilon is generally defined as a small positive number (e.g. 0.00000001, sometimes written 1e-8).

New developers often try to write their own “close enough” function like this:

```C++
#include <cmath> // for std::abs()
 
// epsilon is an absolute value
bool isAlmostEqual(double a, double b, double epsilon)
{
    // if the distance between a and b is less than epsilon, then a and b are "close enough"
    return std::abs(a - b) <= epsilon;
}
```

While this function can work, it's not great. An epsilon of *0.00001* is good for inputs around *1.0*, too big for inputs around *0.0000001*, and too small for inputs like *10,000*. This means every time we call this function, we have to pick an epsilon that's appropriate for our inputs. If we know we're going to have to scale epsilon in proportion to our inputs, we might as well modify the function to do that for us.

[Donald Knuth](https://en.wikipedia.org/wiki/Donald_Knuth), a famous computer scientist, suggested the following method in his book “The Art of Computer Programming, Volume II: Seminumerical Algorithms (Addison-Wesley, 1969)”:

```C++
#include <cmath> // std::abs
#include <algorithm> // std::max
 
// return true if the difference between a and b is within epsilon percent of the larger of a and b
bool approximatelyEqual(double a, double b, double epsilon)
{
    return (std::abs(a - b) <= (std::max(std::abs(a), std::abs(b)) * epsilon));
}
```

Note that while the approximatelyEqual() function will work for most cases, it is not perfect, especially as the numbers approach zero:

One way to avoid this is to use both an absolute epsilon (as we did in the first approach) and a relative epsilon (as we did in Knuth's approach):

```C++
// return true if the difference between a and b is less than absEpsilon, or within relEpsilon percent of the larger of a and b
bool approximatelyEqualAbsRel(double a, double b, double absEpsilon, double relEpsilon)
{
    // Check if the numbers are really close -- needed when comparing numbers near zero.
    double diff{ std::abs(a - b) };
    if (diff <= absEpsilon)
        return true;
 
    // Otherwise fall back to Knuth's algorithm
    return (diff <= (std::max(std::abs(a), std::abs(b)) * relEpsilon));
}
```

