内存四区的概念

text segment
data segment
stack
heap

函数返回字符数组和字符串指针的区别

```c
#include <stdio.h>
char *getstring(){
    // char myname[] = "maxiaolin";		//字符串存储在常量区，但会拷贝一份到字符数组中
    char *myname = "maxiaolin";			//myname直接指向字符串常量区
    return myname;
}

void test(){
    char *pstr = getstring();
    printf("%s\n", pstr);
}

int main(int argc, char *argv[]){
    test();
    return 0;
}
```

函数分配空间

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

//不能改变test02函数中p的指向
void allocate_space01(char *pstr){
    pstr = (char *)malloc(sizeof(char) * 100);
    memset(pstr, 0, 100);
    strcpy(pstr, "hello world");
}

void allocate_space02(char **pstr){
    *pstr = (char *)malloc(sizeof(char) * 100);
    memset(*pstr, 0, 100);
    strcpy(*pstr, "hello world");
}

void test02(){
    char *p = NULL;
    allocate_space01(p);
    printf("%s\n", p);

    allocate_space02(&p);
    printf("%s\n", p);
    free(p);
    p = NULL;
}

int main(int argc, char *argv[]){
    test02();

    return 0;
}
```

变量存储类别

- 全局静态变量和局部静态变量都存储在静态区，其生命周期在程序运行期间有效
- 局部静态变量的可见范围仅限当前函数内部，全局静态变量可见范围从定义到文件结尾``

```c
//头文件不参与编译，每一个.c文件是一个编译单元
int a = 10;				//外部链接，项目下的所有文件可见
//extern int a = 10; 默认外部链接
static int b = 20;		//内部链接属性，只在本文件内可见
```

const 全局变量在常量区，不能修改(直接或间接都不允许)

```c
const int g_c = 200;	//const全局变量
void test03(){
    int *p = (int *)&g_c;
    *p = 20; 			//不允许修改
}
```

const局部变量放在栈帧区，可以通过间接修改

```c

void test03(){
    const int l_c = 200;	//const全局变量
    int *p = (int *)&l_c;
    *p = 20; 				//可以修改
}
```

