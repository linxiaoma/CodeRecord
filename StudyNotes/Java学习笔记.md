## Java学习笔记

### 软件安装

建议安装JDK1.8或11版本，安装完成后需要将Java的安装目录添加到系统环境变量中。

开发工具选择：刚开始学习时可以使用Notepad++编辑器。

企业中主要使用`InteliJ IDEA`，昔日的主流开发软件`Eclipse`占有的市场份额不断减少。

第一个Java程序

```Java
public class HelloWorld {
	
	public static void main(String[] args){
		System.out.println("Hello, world! Hello, Java");
	}
}
```



命令行进行编译执行Java程序时，需要完成以下步骤：

```Java
javac HelloWorld.java	//编译Java源文件
java HelloWorld			//执行编译后的HelloWorld.class程序
```

Note：Java源文件中可以有多个类，但只能有一个类被public关键字修饰，而且被public修饰的类必须与文件名字一样。另外，在高版本JDK中，可以只用执行java文件`java HelloWorld.java`。

扩展：`classpath`和`JAVA_HOME`环境变量。添加`JAVA_HOME`到环境变量，主要是方便其他程序使用Java环境。譬如一些数据库GUI软件需要使用`JRE`。添加`classpath`其实没什么用处。

三种注释方法

- 单行注释
- 多行注释
- 文档注释。一般文档注释可以配合jdk提供的javadoc一起使用，可以对文档注释进行解析，生成一套html文档，对源程序进行说明

Chapter 9 Classes and Objects

A class defines the properties and behaviors for objects.

