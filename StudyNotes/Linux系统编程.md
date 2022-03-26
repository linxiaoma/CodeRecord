# Linux系统编程

***

Part One 基础 

***

Linux基础命令

VIM的使用和GCC编译器

GDB调试和Makefile的编写

文件IO操作

进程相关

进程间通信

信号相关

守护进程和线程

线程同步

Terminal的使用

快捷键打开终端 Ctrl+Alt+T

在终端打开新的窗口 Ctrl+Shit+T

多个终端窗口间切换 Ctrl+PgDn

终端命令

​	history-------------显示历史命令列表 

​								上下箭头可以切换历史命令，也可以用ctrl+n向下遍历，ctrl+p向上遍历

​	在终端提示字符间移动光标：

​			Ctrl+a(home)----移动到头部

​			Ctrl+e(end)----移动到尾部

​			Ctrl+b----向前移动一个字符

​			Ctrl+f----向前移动一个字符

​			Ctrl+h(backspace)----删除光标前边字符

​			Ctrl+d----删除光标后边(光标盖住)字符

​			Ctrl+u----删除光标前边所有字符

​			Ctrl+k----删除光标后边所有字符

tab键补全命令或补全路径，存在多个选项时，连续按下两次tab键会显示候选项

Linux系统目录结构

- 根目录/：所有目录的父目录
  - /root：系统管理员目录
  - /bin：存放常用命令的可执行文件
  - /boot：存放的是启动Linux时使用的一些核心文件，包括一些链接文件及镜像文件
  - /dev：Device的缩写，该目录下存放的是Linux的外部设备，Linux下一切皆文件
  - /etc：系统管理所需要的配置文件和子目录
  - /home: 所有用户的主目录，每一个用户都有一个目录
  - /lib：存放着系统最基本的动态链接共享库
  - /lost+found
  - /media：Linux系统会自动识别一些设备，
  - /mnt：临时挂在别的文件系统
  - /opt：额外安装软件
  - /proc
  - /sbin
  - /selinux
  - /usr：应用程序和文件都放在这个目录下
  - /usr/bin; /usr/sbin; /usr/src
  - /var

用户目录

绝对路径：从根目录/开始

相对路径：相对于当前工作目录而言

​	. 当前目录

​	.. 当前的上一级目录

shell命令提示信息：用户名@主机名:当前目录$ ($---表示普通用户 #---表示root用户)

sudo apt install tree 安装tree程序后可以以树形结构显示文件目录

Note：文件或目录颜色(一般情况) 白色---普通文件 蓝色---目录 绿色---可执行文件 红色---压缩文件 青色---链接文件 黄色---设备文件(block块、char字符、fifo管道) 灰色---其他文件

ls命令

- 命令参数-l
- 命令参数-a 显示隐藏文件

![image-20220217165732368](C:\Users\linxi\AppData\Roaming\Typora\typora-user-images\image-20220217165732368.png)

目录的大小默认是4096字节

cd命令			切换目录

mkdir命令	创建目录

rmdir命令	删除空目录

rm命令

touch命令 

- 如果文件不存在，则创建文件
- 如果文件存在，则修改文件访问时间

cp命令

- 拷贝文件 cp 文件名 new_file。注意文件存在与不存在的区别
- 拷贝目录 cp directory new_directory。注意目录存在与不存在的区别

cat命令

more命令

less命令

head命令

tail命令

mv命令

- 移动目录或文件
- 重命名目录或文件

软链接和硬链接

- 软连接
- 硬链接 ln hard.txt hard.txthd.指向硬盘上的同一个inode.硬链接计数

目录不能创建硬链接

文件或目录属性

- wc 获取文本文件的信息，包括行数-单词个数-字节数
- od 查看二进制文件信息，-t 指定数据的的显示格式
- du命令 查看当前目录下文件大小 -h
- df 磁盘空间使用量 -h
- which查看使用的命令路径(查看外部命令)

文件权限、用户、用户组

- whoami查看当前用户
- 修改权限
    - 文字设定法：chmod [who] [+-=] [mode]
        - who
            - u 文件所有者
            - g 文件所属组
            - o 其他人
            - a 所有人
        - 添加权限 + 
        - 减少权限 -
        - 覆盖原来的权限 =
        - mode
            - r：读
            - w：写
            - x：执行
    - 数字设定法
        - 没有权限 -
        - 读权限 r---4
        - 写权限 w---2
        - 执行权限 x---1
        - chmod 权限对应的数字 文件名 （三位数字分别对应文件所有者、文件所属组、其他人）
            - chmod -001 temp去掉其他人对文件的执行权限

软件的安装和卸载

1、在线安装

- 安装：sudo apt(apt-get) install 安装包的名字
- 卸载：sudo apt(apt-get) remove 软件的名字(安装包安装后名字可能会改变)
- 软件列表更新：sudo apt update
- 更新软件：sudo apt upgrade
- 清空缓存：sudo apt clean
    - /var/cache/apt/archives(缓存目录)

2、软件包安装(Ubuntu下 .deb格式)

将.deb格式的软件包下载到本地。

- 安装：sudo dpkg -i 安装包.deb		# dpkg表示deb的package
- 卸载：sudo dpkg -r 软件的名字

3、源码安装

- 阅读readme文档，了解安装步骤和方法
- 执行./configure
- make #编译
- sudo make install

VIM使用

VIM的三种工作模式

- 命令模式
- 编辑模式
- 末行模式

光标的移动

​	上下左右 HJKL

​	光标移动到行首：0(零)

​	光标移动到行位：$

​	快速移动到文件第一行行首：gg

​	快速移动到文件最后一行首：G

​	快速定位某一行：行号G

​	按行数跳动：行数+enter

编辑操作	

​	删除光标后面的字符：x

​	删除光标前面的字符：X

​	撤销操作：u

​	反撤销操作：ctrl+r

​	删除一个单词：dw(光标在单词的第一个字符，如果不在第一个字符上，则删除光标后面的单词部分)

​	删除光标前半部分：d0

​	删除光标前半部分：d$或D

​	删除(剪切)整行：dd

​	删除(剪切)多行：行数dd

​	删除整个文档：dG(光标在第一行)或dgg(光标在最后一行)

​	复制一行：yy

​	复制多行：nyy	

​	复制半行：切换到可视模式 v，然后再用光标移动选择文本，复制，粘贴

​	粘贴在光标前面：P

​	粘贴在光标后面：p

​	替换：r替换光标盖住的字符

​				R从光标盖住位置一直替换后面的字符

​	查找：命令模式下 /findstr 				n向下调到下一个查找到的字符串
​																N向上调到下一个查找到的字符串
​				?findstr

​				根据文档中的关键字进行搜索：光标移动到待搜索关键字上，键盘输入#
​				查询代码函数文档(man文档)：3K 3表示章节号

​	查询帮助文档：man 指定章节号(章节号内容说明)
​				1 shell命令
​				2 系统调用
​				3 库调用
​				4 特殊文件
​				5 文件格式和规范
​				6 游戏
​				7 杂项
​				8 系统管理命令(通常只针对root用户)
​				9 内核例程

文件的创建和保存

保存方法：

- ZZ(连续按两次Z)

代码格式化: gg=G

文本编辑模式

​	从命令模式切换到文本编辑模式

			- a 从光标后开始插入字符
- A 行尾
- i 光标前插入
- I 行首
- o 在光标所在行下插入新行
- O 在光标所在行上插入新行
- s 删除光标盖住的字符，再插入
- S 删除光标所在的行

末行模式

​		在命令模式下输入:进入末行模式

​		:q退出

​		:q!退出不保存

​		:w保存

​		:wq保存退出 或者:x 在命令模式下保存退出ZZ，如果是新文档，不保存。在末行模式下保存退出会保存新文档。

​		在末行模式按两次esc切换到命令模式

​		末行模式查找替换操作：

​				:s/当前字符串/替换字符串 		 替换光标所在行一个字符串				

​				:s/当前字符串/替换字符串/gc		 替换光标所在行的所有查找到的字符串  c表示替换时显示提示信息

​				:22,28s/当前字符串/替换字符串/g		指定范围替换

​				:%s/当前字符串/替换字符串/g		全部替换

​		分屏操作

​				:sp	水平分屏

​				:q 退出光标所在的屏

​				:vsp 垂直分屏。屏之间切换ctrl+ww。退出所有屏:qall，保存所有:wall

​				:vsp hello.c打开新的文件，并分屏

​				vim -on hello.c stdio.h打开两个文件时进行水平分屏，n表示分屏的数目，可以省略

​				vim -O hello.c stdio.h打开两个文件时进行垂直分屏，n表示分屏的数目，可以省略

​	执行shell命令

​			:!ls		感叹号+shell命令，按Enter回到Vim

VIM配置文件

- 用户级别
    - ~/.vimrc
- 系统级别
    - /etc/vim/vimrc

GCC

gcc工作流程

- 预处理	-E
    - 宏替换
    - 头文件展开
    - 去掉注释
    - xxx.c生成xxx.i

- 编译     -S
    - xxx.i--->xxx.s
    - 汇编文件
- 汇编    -c
    - xxx.s->xxx.o
    - 二进制文件
- 链接
    - 将函数库中的相应代码链接到目标文件中，生成可执行文件
    - gcc xxx.o -o hello生成指定文件名的可执行文件

gcc常用参数

- -v/--version：查看gcc版本信息
- -I：编译时指定头文件路径  gcc sum.c -I ./include/ -o sum
- -c：生成xxx.o文件
- -o：指定生成的文件名字
- -g：gdb调试时需要加的参数，生成的文件比未加-g文件大。类似release和debug版本
- -D：在编译时指定一个宏  gcc sum.c -I ./include/ -o sum -D DEBUG在编译时定义DEBUG宏(使用场景)
- -Wall：显示所有警告信息
- -On：设置代码优化级别(Optimization)，n是优化等级：1->2->3逐渐增加

静态库和动态库

库是什么？二进制文件

库如何使用

- 头文件
- 制作出的库

静态库的制作和使用(.a结尾)

- 命名规则：libtest.a
    - lib
    - xxx-库的名字
    - .a
- 制作步骤
    - 准备源代码  .c或.cpp
    - 将.c文件生成.o   gcc xxx.c yyy.c zzz.c -c 会将每一个.c文件生成对应的.o文件
    - 将.o文件打包
        - ar --archive 
        - 命令格式 ar rcs 静态库的名字 .o文件
        - example：ar rcs libmycalc.a add.o sub.o mul.o div.o
- 库的使用
    - 将制作好的库放入lib文件夹里
    - 编译指令：gcc main.c -I ./include/ -L ./lib -lmycalc -o app #指定头文件路径，指定库的路径，指定库的名字

动态库的制作和使用(.so结尾)

- 命名规则

    - libxxx.so

- 制作步骤

    - 将源文件生成.o

        gcc a.c b.c c.c -c -fpic(fPIC) #添加-fpic参数

    - 打包

        gcc -shared -o libxxx.so a.o b.o c.o

- 使用方法

    - 头文件xxx.h

    - 动态库libxxx.so

    - 参考函数声明编写测试程序main.c

        ​	gcc main.c -I ./include/ -L ./lib -l mycalc -o app #指定头文件路径，指定库的路径，指定库的名字

    - 动态库加载失败问题分析

        - 查看文件格式 file app。ELF格式的可执行文件，是由ld-linux-x86-64.so.2来链接的，它先后搜索elf文件的DT_PRATH段->环境变量LD_LIBRARY_PATH->/etc/ld.so.cache文件列表->/lib/，/usr/lib目录，找到库文件后将其载入内存
        - ldd app 查看应用程序运行时需要加载哪些库文件
        - env 查看环境变量
        - echo $PATH：显示PATH环境变量内容

    - 如何让系统找到共享库

        - 拷贝自己的库到/lib或者/usr/lib。不推荐，容易和系统的库文件名字冲突

        - 使用环境变量

            - 临时设置--终端关闭就失效了

                在终端：export LD_LIBRARY_PATH=动态库的路径。export导入

                在终端：export LD_LIBRARY_PATH=./lib:$LD_LIBRARY_PATH。 #将./lib拼接到LD_LIBRARY_PATH环境变量中

            - 永久设置

                - 用户级别
                    - ~/.bashrc
                        - 配置完成：重启终端
                        - 或者source ~/.bashrc
                - 系统级别
                    - /etc/profile

        - /etc/ld.so.cache文件列表

            - 找到一个配置文件
                - /etc/ld.so.conf
                - 把动态库的绝对路径添加到文件中
            - 执行一个命令
                - sudo ldconfig -v

    - 知识点拓展

        - dlopen，dlclose，dlsym

