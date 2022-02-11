## Chapter O1 Bit Manipulations

On modern computer architectures, the smallest addressable unit of memory is a byte. Since all objects need to have unique memory addresses, this means objects must be at least one byte in size. For most variable types, this is fine. However, for Boolean values, this is a bit wasteful. Boolean types only have two states: true (1), or false (0). This set of states only requires one bit to store. However, if a variable must be at least a byte, and a byte is 8 bits, that means a Boolean is using 1 bit and leaving the other 7 unused.

std::bitset provides 4 key functions that are useful for doing bit manipulation:

- test() allows us to query whether a bit is a 0 or 1
- set() allows us to turn a bit on (this will do nothing if the bit is already on)
- reset() allows us to turn a bit off (this will do nothing if the bit is already off)
- flip() allows us to flip a bit value from a 0 to a 1 or vice versa

Here's an example:

```C++
#include <bitset>
#include <iostream>
 
int main()
{
    std::bitset<8> bits{ 0b0000'0101 }; // we need 8 bits, start with bit pattern 0000 0101
    bits.set(3); // set bit position 3 to 1 (now we have 0000 1101)
    bits.flip(4); // flip bit 4 (now we have 0001 1101)
    bits.reset(4); // set bit 4 back to 0 (now we have 0000 1101)
 
    std::cout << "All the bits: " << bits << '\n';
    std::cout << "Bit 3 has value: " << bits.test(3) << '\n';
    std::cout << "Bit 4 has value: " << bits.test(4) << '\n';
 
    return 0;
}
```

**What if we want to get or set multiple bits at once?** std::bitset doesn’t make this easy. In order to do this, or if we want to use unsigned integer bit flags instead of std::bitset, we need to turn to more traditional methods. We’ll cover these in the next couple of lessons.

C++ provides 6 bit manipulation operators, often called **bitwise** operators:

| Operator    | Symbol | Form   | Operation                          |
| :---------- | :----- | :----- | :--------------------------------- |
| left shift  | <<     | x << y | all bits in x shifted left y bits  |
| right shift | >>     | x >> y | all bits in x shifted right y bits |
| bitwise NOT | ~      | ~x     | all bits in x flipped              |
| bitwise AND | &      | x & y  | each bit in x AND each bit in y    |
| bitwise OR  | \|     | x \| y | each bit in x OR each bit in y     |
| bitwise XOR | ^      | x ^ y  | each bit in x XOR each bit in y    |

---

The **bitwise left shift** (<<) operator shifts bits to the left. The left operand is the expression to shift the bits of, and the right operand is an integer number of bits to shift left by.

So when we say `x << 1`, we are saying "shift the bits in the variable x left by 1 place". New bits shifted in from the right side receive the value 0.

0011 << 1 is 0110
0011 << 2 is 1100
0011 << 3 is 1000

Note that in the third case, we shifted a bit off the end of the number! Bits that are shifted off the end of the binary number are lost forever.

The **bitwise right shift** (>>) operator shifts bits to the right.

1100 >> 1 is 0110
1100 >> 2 is 0011
1100 >> 3 is 0001

Note that in the third case we shifted a bit off the right end of the number, so it is lost.

Note that if you're using operator << for both output and left shift, parenthesization is required.

The **bitwise NOT** operator (~) is perhaps the easiest to understand of all the bitwise operators. It simply flips each bit from a 0 to a 1, or vice versa. Note that the result of a *bitwise NOT* is dependent on what size your data type is.

Flipping 4 bits:
~0100 is 1011

Flipping 8 bits:
~0000 0100 is 1111 1011

**bitwise XOR** (^), also known as **exclusive or**. When evaluating two operands, XOR evaluates to *true (1)* if one *and only one* of its operands is *true (1)*. If neither or both are true, it evaluates to *0*.

A **bit mask** is a predefined set of bits that is used to select which specific bits will be modified by subsequent operations.