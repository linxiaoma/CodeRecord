# IDEA使用介绍

---

## 一、安装步骤

1. 版本选择

    Ultimate版本与Community版本

2. License管理

    使用教育邮箱可以免费使用

3. 软件环境

    需要先安装java jdk

4. 软件的卸载

    直接用卸载工具进行卸载

---

## 二、软件结构层次

在Eclipse中需要先设置Workspace，在Workspace中建立项目。

在IDEA中，建立的Project相当于Eclipse中的工作空间，Eclipse中的项目相当于IDEA中的Module，同一个工程下可以有多个模块。

IDEA的结构层次：项目包含多个模块，单个模块包含包，单个包含有多个类

out目录放的是编译后的字节码文件

模块的删除：先移除模块，之后会显示delete菜单项，再delete。目的是防止误删除操作。

<img src="C:\Users\linxi\AppData\Roaming\Typora\typora-user-images\image-20211115215323003.png" alt="image-20211115215323003" style="zoom: 67%;" />



## 三、IDEA常用设置

File->setting进入设置选项(Ctrl+Alt+s)。或者点击工具栏上的扳手图标

主题选择，字体选择，设置字体大小

手动导包：Alt+Enter

自动导包：

![设置自动导包](D:\Study\学习笔记\Images\IDEA自动导包.png)

显示行号

![显示行号](D:\Study\学习笔记\Images\IDEA设置行号和方法分隔符.png)

代码提示时忽略大小写

![代码提示忽略大小写](D:\Study\学习笔记\Images\IDEA忽略大小写提示.png)

多个类不隐藏，多行显示



修改注释颜色，默认是灰色



修改类头文档信息(代码模板)



修改编码格式



自动编译，确保修改后字节码文件是最新的

![image-20220115155730983](C:\Users\linxi\AppData\Roaming\Typora\typora-user-images\image-20220115155730983.png)



省电模式

![img](file:///C:/Users/linxi/AppData/Local/Temp/SNAGHTML1977004.PNG)

分屏显示



导入jar包

File->Project Structure，从Libraries中点击+，选择Java，选择Jar包的位置进行导入

![image-20220115160941138](C:\Users\linxi\AppData\Roaming\Typora\typora-user-images\image-20220115160941138.png)

![image-20220115161233790](C:\Users\linxi\AppData\Roaming\Typora\typora-user-images\image-20220115161233790.png)

导入的jar包在External Libraries下

生成序列化版本号





## 四、IDEA常用快捷键

- 创建内容 Alt+Insert

- main方法 psvm

- 输出语句 sout，输出字符串可以用字符串.sout

- 复制一行 Ctrl+d

- 撤回操作或删除一行 Ctrl+y  撤回操作Ctrl+Shift+z(Ctrl+z)

- 代码上下移动 Ctrl+Shift+上下箭头

- 搜索类 ctrl+n

- 生成代码 Alt+Insert

- 导包、生成变量 Alt+Enter

- 单行或多行注释 Ctrl+/  Ctrl+Shift+/

- 重命名 Shift+F6

- for循环 fori或forj

- 代码块包围 Ctrl+Alt+T，放到if或for循环代码块

- 代码自动补全 Alt+/

- 缩进 tab，取消缩进 shift+tab

- 显示导航栏 Alt+1

- 查看源码：Ctrl+鼠标左键

- 代码一层一层调用  Ctrl+Alt+向左箭头  Ctrl+Alt+向左箭头 

- 显示代码的结构 Alt+7

## 五、常用代码模板

Live 实时代码模板，直接输入按回车即可

postfix 后缀代码模板，使用变量.模板

- main方法

    - main
    - psvm

- 输出语句

    - sout，soutv(打印变量)，soutp(打印方法的形参)，soutm(打印方法的名字)
    - 输出变量，使用变量.sout

- 循环

    - 普通for循环：fori(正向)，.fori(正向)，forr(逆向)
    - 增强for循环：iter 或者变量.for

- 条件判断

    - 是否等于null：ifn 或者 变量.null (if null)
    - 是否不等于null：inn 或者 变量.nn (if not null)

- 属性修饰符

    - prsf：private static final
    - psf: public static final

- 修改现有的模板

    - 可以更改Live Templates，按照自己的需求修改对应的模板

        - 修改plain->psfi如下

            ```java
            public static final int $VAR1$ = $VAR2$;
            $END$
            ```

        - 添加自己的代码模板

            - ![添加模板](D:\Study\学习笔记\Images\IDEA添加模板.png)

                模板添加完成后，还需要设置应用到那种语言。$$定义光标的位置，按Enter键切换到下一个$$位置

            - 设置方法注释模板

                ![IDEA自定义模板](D:\Study\学习笔记\Images\IDEA自定义模板.png)

## 六、断点调试

断点调试之前，选择针对Windows的内存优化设置。

![内存优化设置](D:\Study\学习笔记\Images\IDEA断点调试内存优化.png)

断点调试功能键

![](D:\Study\学习笔记\Images\IDEA调试功能键.png)

断点调试时加入if判断

右键点击断点，可以设置相应的条件

![](D:\Study\学习笔记\Images\IDEA断点设置条件判断.png)

调试时查看变量的值

Alt+F8或者右键点击选择Evaluate Expression...