## `Qt`教程

Qt Creator是一个跨平台的、完整的Qt集成开发环境(IDE)，可以运行在Windows、Linux以及Mac OS操作系统上。提供了高级C++代码编辑器、项目和生成管理工具、集成的上下文相关的帮助系统、图形化调试工具、代码管理和浏览工具等。

Qt发展历史



Qt下载与安装





Qt Creator环境介绍

主要由主窗口区、菜单栏、模式选择器、构件套件选择器、定位器和输出窗格等几部分组成。

- 菜单栏

  - 文件菜单
  - 编辑菜单
  - 构件菜单
  - 调试菜单
  - Analyze菜单
  - 工具菜单
  - 空间菜单
  - 帮助菜单

- 模式选择器(Mode Selector)

  - 欢迎
  - 编辑
  - 设计
  - 调试
  - 项目
  - 帮助

- 构件套件选择器(Kit Selector)

  - 目标选择器(Target Selector)
  - 运行按钮(Run)
  - 调试按钮(Debug)
  - 构件按钮(Building)

- 定位器(Locator)

  快速定位项目、文件、类、方法、帮助文档以及文件系统。可以使用过滤器更加准确地定位要查找的结果、可以在“工具->选项”菜单项中设置定位器的相关选项。

- 输出窗格

  输出窗格包含了问题、搜索结果、应用程序输出、编译输出、Debugger Console、概要信息、版本控制7个选项。它们分别对应一个输出窗口，相应的快捷键依次是Alt+数字1~7。

Qt Assistant是可配置且可重新发布的文档阅读器，可以方便地进行定制，并与Qt应用程序一起重新发布。

#### 第一个Qt程序 Hello Qt

掌握Qt项目建立、编译、运行和发布的整个流程。





#### 另一个视角：从零开始创建`Qt`程序

`Windows`下

打开`Qt Creator`并新建项目，选择“其他项目”中的`Empty qmake Project`。在项目文件`.pro`中添加如下的一行代码：`greaterThan(QT_MAJOR_VERSION, 4): QT += widgets`。该行代码的含义是：如果`Qt`主版本大于4，则需要添加widgets模块。其实，直接使用`QT += widgets`也是可以的，但是为了保持和`Qt4`的兼容性，推荐使用这种方式。

接下来，往项目中添加`main.cpp`文件。

编写源代码

```C++
#include<QApplication>

int main(int argc, char** argv)
{
	QApplication app(argc, argv); //任何一个Qt Widgets程序都需要一个QApplication类对象
	return app.exec();
}
```



`Ubuntu`下

创建独立的目录，进入该目录，创建main.cpp

```shell
mkdir Hello
cd Hello
vim main.cpp
```

在新建的`main.cpp`文件中，编写主函数

```c++
#include<QApplication>
#include<QLabel>		//使用QLabel控件时应包含的头文件

int main(int argc, char** argv)
{
	QApplication app(argc, argv); //任何一个Qt Widgets程序都需要一个QApplication类对象
	QLabel label("我是标签");
	label.show();		//控件默认是隐藏的
	return app.exec();	//应用程序进入事件循环
}
```

构建项目

命令行输入 `qmake -project`。生成和工程名一样的工程文件`Hello.pro`

打开`Hello.pro`文件，添加 `QT += widgets`

执行`qmake`，生成`Makefile`文件

执行`make`，生成可执行文件`hello`

最后运行可执行文件`./hello`

往项目中添加.ui文件

在模板中选择Qt中的Qt Designer Form，在选择界面模板时选择Dialog without Buttons项，并命名文件。



### `Qt`中文编码

---

UCS转换格式(UCS Transformation Format, UTF)

默认情况下，`Qt5`可以正确理解`utf-8`格式编码，并将其自动转换为内部的`unicode`编码，如果使用utf-8的中文字符串总是能够正确显示，但是如果使用其他格式的外部编码，比如`Windows`中常用的`GBK`编码，将会出现乱码现象。

通过使用`QTextCodec`实现编码转换

```C++
#include<QTextCodec>
//将非unicode编码的字符串转为unicode编码
//创建QTextCodec对象
QTextCodec *coder = QTextCodec::codecForName("gbk");
QLabel label(coder->toUnicode("我是标签"));

//QObject::tr()函数可以实现多语言支持，建议程序中所有要显示到界面上的字符串都使用函数括起来。尽管该方法可以实现中文显示，不过还是建议正规编写代码时全部使用英文，最后使用国家化方法来实现中文显示。
```

父窗口

窗口之间存在相互依赖的关系。通过指定父窗口，可以将新创建的窗口指定停靠。实现对控件的统一管理。`QWidget`及其子类的对象都可以当做父窗口。

常用的父窗口

- `QWidget`
- `QMainWindow`(主窗口带菜单栏和工具栏)，是`QWidget`的直接子类
- `QDialog`(对话框)，也是`QWidget`的直接子类

设置窗口的位置和大小

```c++
void move(int x, int y);	//指定x，y坐标
void resize(int w, int h);	//指定窗口的宽和高
void setFixSize(); //设置固定宽高的窗口，不能在边界处对窗口进行大小拖拉
```

`Qt`对象树概念



`Qt`窗口的坐标系



### 信号与槽

---

信号和槽通过`connect`函数进行连接，遵循松散耦合原则。`connect`函数的语法如下

```C++
//connect(信号的发送者, 发送的信号(signal), 信号接收者,信号处理(槽函数));
QObject::connect(const QObject *sender, const char *signal, const QObject *receiver, const char *method, Qt::ConnectionType type = Qt::AutoConnection)
```









### 自定义信号与槽 

在`Qt`中添加类的时候，通常从`QObject`类派生。这样添加的类就可以使用`Qt`提供的信号与槽功能。

自定义信号 写到`signals`下。返回值是`void`，只需要声明，不需要实现。可以有参数，可以重载。

槽函数需要写在`public slots`下。对于高版本的`Qt`(5.4版本后)，槽函数可以写在`public`段下。返回值类型为`void`，需要声明，也需要实现。

发出信号使用`emit()`函数。

信号与槽发生重载时，需要使用函数指针明确指向信号和槽。

```C++
void (Teacher::*pteacher)(QString) = &Teacher::hungry;
void(Student::*pstu)(QString) = &Student::treat;
```



注意：`QString`转`char*`

```C++
//将QString类型转换成char*，这样可以去掉输出语句中QString类型中包含的""
//先使用.toUtf8()转成QByteArray，再使用.data()转成char*
qDebug() << "请老师吃" << foodName.toUtf8().data();
```

扩展

```C++
/* 拓展：
 *      1、信号可以连接信号
 			当第一个信号发出时，第二个信号被发出。除此之外，这种信号-信号的形式和信号-槽的形式没有区别
 *      2、一个信号可以连接多个槽函数
 			槽会一个接一个的被调用，但是它们的顺序是不确定的
 *      3、多个信号可以连接一个槽函数
 			只要任意一个信号发出，这个槽就会被调用
 *      4、信号和槽函数的参数要一一对应
 *      5、信号参数个数可以多余槽函数参数个数，可以忽略信号的参数
 		6、信号和槽之间可以断开连接
 */


```

断开信号`disconnect`

`Qt4`版本信号与槽之间的连接

```C++
//Qt4版本。优点：参数直观，缺点：类型不做监测。推荐使用Qt5
//The connection way between signals and slots of Qt5 is recommended.

connect(信号发送者, SIGNAL(发送的信号), 信号接收者, SLOT(槽函数));
//SIGNAL和SLOT宏把函数名字直接转成了字符串，
```

### `lambda`表达式

`C++11`中的`lambda`表达式用于定义并创建匿名的函数对象，简化编程工作。

注意：早期`Qt`版本如果要使用`lambda`表达式，需要在工程文件中添加代码：`CONFIG += c++11`。

`lambda`表达式的基本构成

```C++
[capture](parameters) mutable ->return-type{
	function body
}
/*
	[]标志lambda表达式开始，不能省略。用于捕获参数
		空 没有使用任何函数对象参数
		= 值传递参数。函数体内可以使用lambda所在作用范围内所有可见的局部变量(包括lambda所在类的this)，并且是值传递方式(相当于编译器自动按值传递了所有局部变量)
		& 引用传递参数。与=的差别是引用传递
		this 函数体内可以使用lambda所在内的成员变量
		a 变量a按值传递到lambda函数体，其他变量无法在函数体内访问
		&a 变量a按引用传递到lambda函数体
		
	mutable 可以修改按值传递的变量拷贝，并没有修改原变量

*/
```

QMainWindow

一个窗口最多只有一个菜单栏，位于主窗口顶部、主窗口标题栏下面。

菜单栏的层次关系

​	菜单栏(<font color='red'>`QMenuBar`</font>)---添加菜单(QMenu)----每个菜单里有菜单项(QAction)

```C++
//创建菜单栏 Header: #include <QMenuBar> 

//The QMenuBar class provides a horizontal menu bar.
QMenuBar* menuBar = new QMenuBar(this); //将菜单栏添加到主窗口的对象树中
//将菜单栏添加到窗口
this->setMenuBar(menuBar);

//添加菜单 #include <QMenu> 
QMenu *file = new QMenu("文件",this);		//创建文件菜单
QMenu *edit = new QMenu("编辑",this);		//创建编辑菜单
menuBar->addMenu(file);		//把文件菜单添加到菜单栏中
menuBar->addMenu(edit);		//把编辑菜单添加到菜单栏中

//添加菜单项 #include <QAction> 
//创建file菜单内的菜单项
QAction *new_file = new QAction("新建", this);
QAction *open_file = new QAction("打开", this);
QAction *save_file = new QAction("保存", this);
QAction *saveas_file = new QAction("另存为...", this);
//将菜单项添加到file菜单
file->addAction(new_file);
file->addAction(open_file);
file->addAction(save_file);
file->addAction(saveas_file);
//给菜单项设置快捷键

```

官方提供的实例，可以给菜单项添加图标

```C++
const QIcon openIcon = QIcon::fromTheme("document-open", QIcon(":/images/open.png"));
QAction *openAct = new QAction(openIcon, tr("&Open..."), this);
openAct->setShortcuts(QKeySequence::Open);
openAct->setStatusTip(tr("Open an existing file"));
connect(openAct, &QAction::triggered, this, &MainWindow::open);
fileMenu->addAction(openAct);
fileToolBar->addAction(openAct);
```

添加工具栏



添加状态栏



添加铆接部件



添加中心部件
