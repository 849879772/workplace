# Ubuntu下用gcc生成静态库和动态库以及对应应用
@[toc]

---
## 问题描述

>- 一.阅读、理解和学习材料“用gcc生成静态库和动态库.pdf”和“静态库.a与.so库文件的生成与使用.pdf”，请在Linux系统(Ubuntu)下如实仿做一遍。

>- 二.在第一次作业的程序代码基础进行改编，除了x2x函数之外，再扩展写一个x2y函数（功能自定），main函数代码将调用x2x和x2y ；将这3个函数分别写成单独的3个 .c文件，并用gcc分别编译为3个.o 目标文件；将x2x、x2y目标文件用 ar工具生成1个 .a 静态库文件, 然后用 gcc将 main函数的目标文件与此静态库文件进行链接，生成最终的可执行程序，记录文件的大小。

>- 三.将x2x、x2y目标文件用 ar工具生成1个 .so 动态库文件, 然后用 gcc将 main函数的目标文件与此动态库文件进行链接，生成最终的可执行程序，记录文件的大小，并与之前做对比。

-------
<br>
<br>

## 一.在Linux-ubuntu上按照动态库和静态库的要求，用gcc生成.a静态库和.so动态库#### 1.创建main1.c和sub1.c,sub1.h文件
<br>

### （一）.生成.a静态库
<br>

#### 1.创建main1.c和sub1.c,sub1.h文件
- 在一个文件下调出终端，用touch命令创建两个.c文件和一个.h文件
```shell
	touch main.c

	touch hello.c

	touch sub1.h
```
<br>
<br>

####  2.键入代码 
<br>

##### 1. 用vim命令编辑main1.c
```shell
	vim main.c  
```
```c

#include"hello.h"
 int main()
{	
	hello("everyone");
	return 0;
}
```
<br>

##### 2. 用vim命令编辑hello.c,hello.h
```shell
	vim hello.c  
```
```c
#include<stdio.h>
void hello(const char *name)
{
	printf("Hello %s!\n",name);
}
```
----
```shell
	vim hello.h  
```
```c
#ifndef HELLO_H
#define HELLO_H
void hello(const char*name);
#endif //HELLO_H
```
<br>

##### 3.将hello.c编译成.o文件
> 无论静态库，还是动态库，都是由.o文件创建的。因此，我们必须将源程序hello.c通过gcc先编译成.o文件。


```Shell
	gcc -c hello.c
```
<br>

##### 4.由.o文件创建静态库
>- 静态库文件名的命名规范是以lib为前缀，紧接着跟静态库名，扩展名为.a。
><br>
>例如:我们将创建的静态库名为 myhello，则静态库文件名就是 libmyhello.a。在创建和使用静态库时，需要注意这点。创建静态库用ar命令。在系统提示符下键入以下命令将创建静态库文件libmyhello.a。

```Shell
	ar -crv libmyhello.a hello.o libmyhello.a main.c
```
![请添加图片描述](https://img-blog.csdnimg.cn/7d6cdeb43d634927819bb29825388d8d.png)

<br>



##### 5.在程序中使用静态库
> 静态库制作完了，如何使用它内部的函数呢?
> 只需要在使用到这些公用函数的源程序中包含这些公用函数的原型声明，然后在用gcc命令生成目标文件时指明静态库名，gcc将会从静态库中将公用函数连接到目标文件中。
> * 注意，gcc 会在静态库名前加上前缀 lib，然后追加扩展名.a得到的静态库文件名来查找静态库文件。
---
###### 方法一.
```Shel
	gcc -o hello main.c -L. -lmyhello
```
> 注意：对于自定义的静态库，main.c还可以放在-L.和-lmyhello之间，否则myhello没有定义。
-L.：表示连接的库在当前目录
###### 方法二.
```Shel
	gcc main.c libmyhello.a -o hello
```
###### 方法三.
-  先生成main.o
```Shel
	main.o gcc -c main.c
```
- 再生成可执行文件
```Shell
	gcc -o hello main.c libmyhello.a
```
<Br>

##### 6. 所有文件以及运行结果
![请添加图片描述](https://img-blog.csdnimg.cn/a0227ecb61784d78bea85feb71eb43bd.png)
![请添加图片描述](https://img-blog.csdnimg.cn/9737985cc51441bfa5dc4b022204edd5.png)

<Br>

### （二）.生成.so动态库
<br>

##### 1. 由.o文件创建动态库文件
> 动态库文件名命名规范和静态库文件名命名规范类似，也是在动态库名增加前缀**lib**，但其文件扩展名为.**so**。例如:我们将创建的动态库名为myhello，则动态库文件名就是 **libmyhello.so**。用gcc来创建动态库。

```s
	gcc -shared -fPIC -o libmyhello.so hello.o
```
动态文件生成

<Br>

![请添加图片描述](https://img-blog.csdnimg.cn/8a37995896dc4072877d8d7665646be9.png)
##### 2. 在程序中使用动态库
> 在程序中使用动态库和使用静态库完全一样，也是在使用到这些公用函数的源程序中包含这些公用函数的原型声明，然后在用gcc命令生成目标文件时指明动态库名进行编译。我们先运行gcc命令生成目标文件，再运行它看看结果。

```Shell
	gcc -o hello main.c -L. -lmyhello或gcc main.c libmyhello.so -o hello
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9f1638bed1c448af9f788f444f7546a9.png)

![请添加图片描述](https://img-blog.csdnimg.cn/dd65c91de58f48c894d2420c14c20cd6.png)

*出错了。快看看错误提示，原来是找不到动态库文件 libmyhello.so。程序在运行时，会在lusr/lib 和/lib等目录中查找需要的动态库文件。若找到，则载入动态库，否则将提示类似上述错误而终止程序运行。我们将文件 libmyhello.so复制到目录/usr/lib 中，再试。*

<Br>![请添加图片描述](https://img-blog.csdnimg.cn/2812e3d8c36b48289aadaba44d0613ec.png)

----
<br>
<br>

## 二.在第一次实验的基础上实现新的函数，并使用静态库文件生成可执行文件
###  1.	头文件和源文件的键入
- <a id="test1">  add1.c <a/>
```c
#include "add1.h"
float x2y(int a,int b)
{
	float c;
	c=a+b;
	return c;
}
```

- add1.h
```c
#include <stdio.h>
float x2y(int a,int b);
```
- sub1.c
```c
#include "sub1.h"
float x2x(int a,int b)
{
	float c;
	c=a-b;
	return c;
}
```
- sub1.h
```c
#include <stdio.h>
float x2x(int a,int b);
```
- main.c
```c

#include"sub1.h"
#include"add1.h"
void main()
{
	int a=4;int b=1;
	printf("a-b=%f\n",x2x(a,b));
	printf("a-b=%f\n",x2y(a,b));
}
```
<br>

### 2.	生成对应.o文件以及对应静态库文件
![请添加图片描述](https://img-blog.csdnimg.cn/acacf2e4328f447795d5dfbd91486d85.png)
<br>

### 3.	在程序中使用静态库
![请添加图片描述](https://img-blog.csdnimg.cn/81f9777d9008429b8f78ae6c2a46627a.png)<br>
<br>

### 4.	记录库文件大小
![请添加图片描述](https://img-blog.csdnimg.cn/dea52f930cca460f827a7934a1191168.png)


----
<Br>

<br>


## 三.在第一次实验的基础上实现新的函数，并使用动态库文件生成可执行文件，并与静态库可知性文件对比
<br>

### 1.	头文件和源文件的键入
- <a href="#test1">请参考：键入代码</a>
<br>

### 2.	生成对应.o文件以及对应动态库文件
![请添加图片描述](https://img-blog.csdnimg.cn/f5f60144db4c4470b888d57419ba61a7.png)



<br>
### 3.	在程序中使用动态库


> 注意： 要把对应的.so 文件移入/usr/lib/下，否贼会找不到库文件报错

![请添加图片描述](https://img-blog.csdnimg.cn/017f339345f14d188d675a7b6b63cb01.png)
<br>

### 4.	记录库文件大小
![请添加图片描述](https://img-blog.csdnimg.cn/76bcc9a5f9e8488799b0fd8ebac877fd.png)
<br>

### 5. 比较静态库和动态库

* 通过比较发现静态库要比动态库要小很多，生成的可执行文件大小也存在较小的差别。

---
## 四.总结
这次学习静态库和动态库学会把自己的一些函数封装成库，熟悉了gcc工具的应用，以及从.c文件到可知性文件的，预处理，汇编，编译和链接的各种分部的进行。
