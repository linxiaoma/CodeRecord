# C Primer(第六版)学习笔记





## Chapter 11 Character Strings and String Functions

字符串：以\0结尾的字符数组

定义字符串的三种主要方法

- string constatns
- char arrays
- char pointers

A *string literals*, also termed a *string constant*, is anything enclosed in a double quotation marks. The enclosed charaters, plus a terminating \0 charater automatically provided by the compiler, art stored in memory as a character string. Character string constants are placed in the static storage class. The entire quoted phrase acts as a pointer to where the string is stored. This action is analogous to the name of an array acting as a pointer to the array's location.

整个字符串"I am a symbolic string constant."  作为一个指针，指向字符串存储位置。而语句`*(I am a symbolic string constant.") `则是获取字符串的第一个字符。

时刻牢记字符数组和字符串的区别。字符串是以\0结尾的字符数组。

字符串数组初始化

```C
const char m1[40] = "Limit yourself to one line's worth.";
```

该初始化方法是以下数组标准化初始的缩写形式

```C
const char m1[40] = { 'L',
'i', 'm', 'i', 't', ' ', 'y', 'o', 'u', 'r', 's', 'e', 'l',
'f', ' ', 't', 'o', ' ', 'o', 'n', 'e', ' ',
'l', 'i', 'n', 'e', '\", 's', ' ', 'w', 'o', 'r',
't', 'h', '.', '\0'
};
```

标准化初始书写更繁琐，因此字符串初始化通常使用上一种方法。要注意结尾的\0字符，没有它，初始的是字符数组，而不是字符串。另外，在指定数组长度时，确保单元个数至少要比字符串长度多一，用来存储\0字符。任何未使用的元素内容自动会被初始化\0.

通常，在定义字符数组时，让编译器确定字符数组的长度。例如：

```C
const char m2[] = "If you can't think of anything, fake it.";
```

编译器会确定该字符数组的长度，并且会给\0预留存储空间。

使用指针创建数组

```C
const char *pt1 = "Something is pointing at me."
```

```C
const char ar1[] = "Something is pointing at me."
```

要注意指针和数组这两个定义的区别。对于数组定义，字符串常量是存储在**static memory**，它在程序加载进内存就分配完成了。当程序执行到数组定义时，会在栈上分配内存，并拷贝字符串常量初始化这段内存。因此，此时，共有字符串常量的两份拷贝，一份存储在**static memory**，另一份存储在ar1中。之后，编译器将ar1看做为数组首元素的地址，即`&ar1[0]`，而且需要特别注意的是ar1是地址常量，任何试图修改ar1值的操作均是无效的。可以使用`ar1+1`这样的操作访问数组元素地址，但不能使用`ar1++`或`++ar1`这样的操作。增量运算只能运用在变量上，而不能使用在常量。

指针定义同样会在static storage分配相应的空间来存储字符串。一旦程序开始运行，还会给指针变量分配内存空间，其值为字符串的地址。因此，指针最初指向字符串的第一个字符。可以对指针进行增量操作，访问字符串。因为字符串字面量是常量，当pt1指向该字符串时，因将pt1声明为const。const限定不能修改字符串的值，但可以改变pt1的指向。

**简而言之，initializing the array copies a string from static storage to the array, whereas initializing the pointer merely copies the address of the string. **

```c
#define MSG "I'm special."
#include <stdio.h>
int main()
{
    char ar[] = MSG;
    const char *pt = MSG;
    printf("address of \"I'm special\": %p \n", "I'm special");
    printf(" address ar: %p\n", ar);
    printf(" address pt: %p\n", pt);
    printf(" address of MSG: %p\n", MSG);
    printf("address of \"I'm special\": %p \n", "I'm special");
    return 0;
}
```

输出结果为

```C
address of "I'm special": 0x100000f0c
address ar: 0x7fff5fbff8c7
address pt: 0x100000ee0
address of MSG: 0x100000ee0
address of "I'm special": 0x100000f0c
```

上式程序示例表明ar的地址与pt及MSG的地址是不同的。而且，编译器有自由选择存储字符串常量在一个位置或多个位置。

Let’s look further into the matter.  

字符数组、字符串数组、指针数组

```C
const char planets[][8] = {
    "Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune", "Pluto"
};
```

数组的元素都是指向字符串的指针

```C
const char *another_planets[] = {
    "Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune", "Pluto"
};
```

这是定义指针数组，表达的含义是数组，而数组的元素是指针。在使用指针时，类型也不同

```C
char **ptr = another_planets;  //valid
char (*ptr2)[8] = planets; //valid
```

gets和puts函数

It reads an entire line up through the newline character, discards the newline character, stores the remaining characters, adding a null character to create a C string. It’s often paired with puts(), which displays a string, *adding a newline*.   当前。gets函数由于安全性问题已经不再使用(C11标准委员会从标准中废除了gets()函数)。读入字符串时容易发生缓冲区溢出(buffer overflow)So what’s the problem? The problem is that gets() doesn’t check to see if the input line actually fits into the array. Given that its only argument here is words, gets() can’t check. Recall that the name of an array is converted to the address of the first element. Thus gets() only knows where the array begins, not how many elements it has.  

puts函数会在字符串末尾加上换行符。gets()则会丢弃输入中的换行符。如果想把一个字符串读入程序，首先必须预留存储该字符串的空间，然后用输入函数获取该字符串。最简单的方法是，在声明时显示指定数组的大小

```c
char name[81];		//name是一个已分配81字节的地址，可以用来存储字符串
```

读取字符串的函数有：scanf()、gets()、fgets()。

> **注意**：puts()应与gets()配对使用，fputs()应与fgets()配对使用。

使用fgets和fputs

```c
char *fgets(char *str, int n, FILE *stream);
/*
fgets()函数的第2个参数指明了读入字符的最大数量。如果该参数的值是n，那么fgets()将读入n-1个字符，或者读到遇到的第一个换行符为止。如果fgets()读到一个换行符，会把它储存在字符串中。这点与gets()不同，gets()会丢弃换行符。fgets()函数的第3个参数指明要读入的文件。如果读入从键盘输入的数据，则以stdin（标准输入）作为参数，该标识符定义在stdio.h中。
*/
int fputs(const char *str, FILE *stream);
```



```C
fgets(buf, size, stdin); //会读入size-1字符或换行符，上述任一条件满足就会结束
fputs(buf, stdout);		//不会添加换行符
```

自定义输入/输出函数

如果不想使用C库中的函数，可以封装getchar()和putchar()函数来实现自己的输入/输出函数。

字符串函数

常用的字符串处理函数原型放在`string.h`头文件中。

```c
size_t strlen(const char *str);			//返回字符串的长度
char *strcat(char *dest, const char *src)；	//字符串拼接
   
```

`sprintf()`函数吧数据写入字符串，而不是打印在显示器上。该函数可以把多个元素组合成一个字符串。`sprintf()`的第1个参数是目标字符串的地址。其余参数和`printf()`相同，即格式字符串和待写入项的列表。



