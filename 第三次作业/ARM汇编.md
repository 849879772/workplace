> #         **重庆交通大学信息科学与工程学院**
>
> #             **《嵌入式系统基础A》课程**
>
> #                      作业报告（第x周）

**班 级： <span class="underline"> 物联网工程2001 </span>**

**姓名-学号 ： <span class="underline"> 周帅康-632007060321 </span>**

**实验项目名称： <span class="underline"> ARM汇编 </span>**

**实验项目性质： <span class="underline"> 设计性 </span>**

**实验所属课程： <span class="underline">《嵌入式系统基础》 </span>**

**实验室(中心)： <span class="underline"> 南岸校区语音大楼 </span>**

**指 导 教 师 ： <span class="underline">娄路 </span>**

**完成时间： <span class="underline"> 2022</span> 年 <span class="underline"> 10</span> 月 <span class="underline"> 7</span> 日**

------

<div STYLE="page-break-after: always;"></div>

<div STYLE="page-break-after: always;"></div>

**一、实验内容和任务**

**（选做）中值滤波程序设计。在嵌入式系统的数据采样应用中，采集数据收到噪声影响会出现起伏变化，因此经常采取中值滤波算法将干扰数据去除掉。根据提供的资料，写一段 ARM 汇编程序，演示中值滤波算法。**

**二、实验要求**

1\. 分组要求：每个学生独立完成，即1人1组。

2\. 程序及报告文档要求：具有较好的可读性，如叙述准确、标注明确、截图清晰等。

3.项目代码上传github，同时把项目完整打包为zip文件，与实验报告（Markdown源码及PDF文件）、作业博客地址一起提交到学习通。

三. **实验过程介绍 （此处可以填博客内容）**

@[toc]

# ARM汇编-中值滤波实验

<Br>

## 一.中值滤波算法分析

(1)图解

![请添加图片描述](https://img-blog.csdnimg.cn/44f3ed5fe7564e6a99ae9bb7b6fb139c.jpeg)


![请添加图片描述](https://img-blog.csdnimg.cn/f427e51041c94d419bb4ca3e7adebe3a.jpeg)

![请添加图片描述](https://img-blog.csdnimg.cn/7db240004d3f4182838ad81af6463c75.jpeg)


(2)算法解析
> 均值滤波在算法上体现为冒泡排序算法
```c
int arr[3]={1,2,3};//分别赋值
for(ini i = 0;i<n-1;i++)
{
	for(int j = i+1;j<n;j++)
	{
		if(arr[i]>arr[j])
		{
			int t = arr[j];
			arr[j]=arr[i];
			arr[i]=t;
		}
	}
}
```
---
<Br>

## 二.项目创建
(1)打开keil uVision5,并新建一个工程
![请添加图片描述](https://img-blog.csdnimg.cn/1687d1eb6db24f62834ba995fb1ec606.jpeg)
<Br>

(2).建立一个名为Test的工程![请添加图片描述](https://img-blog.csdnimg.cn/78a2ac65bfbd4a26a51cf7a7c69689de.jpeg)

(3).选择STM32F103C8芯片，点击OK![请添加图片描述](https://img-blog.csdnimg.cn/f3de474f792a44959a843bcdf36fe503.jpeg)
<Br>

(4).选择环境的配置，点击OK后完成工程的创建![请添加图片描述](https://img-blog.csdnimg.cn/4f8724ae30b1400e8782f5e10f8bbd8e.jpeg)
<Br>


### 2.新建一个.s文件

<Br>

(1) 工程创建完毕后，对Source Group文件点击右键再点击ADD new item to group
![请添加图片描述](https://img-blog.csdnimg.cn/9bf3a58ffd0546dbb5850228bc35493a.jpeg)
<Br>

(2) 选择文件类型，添加文件
![请添加图片描述](https://img-blog.csdnimg.cn/bbd1fd2809b943bcbb288e24ab17477e.jpeg)


(3).键入代码
>书上的代码缺少了主程序入口以及数组的赋值，我们将它补全后才能运行

```c
 			AREA SORT,CODE,READONLY
		ENTRY
		EXPORT __main
__main
		MOV R0,#7
		LDR R2,=0X40000024
		MOV R9,#1  //后面几次相同操作进行对数组的赋值
		STR R9,[R2]
		MOV R9,#5
		STR R9,[R2,4]
		MOV R9,#8
		STR R9,[R2,8]
		MOV R9,#7
		STR R9,[R2,12]
		MOV R9,#12
		STR R9,[R2,16]
		MOV R9,#15
		STR R9,[R2,20]
		MOV R9,#18
		STR R9,[R2,24]
		SUB R1,R0,#1
		MOV R4,#4
		MLA R3,R1,R4,R2
		SUB R4,R3,#4
LOOP1	ADD R5,R2,#4
LOOP2	LDR R6,[R2]
		LDR R7,[R5]
		CMP R6,R7
		STRHI R6,[R5]
		STRHI R7,[R2]
		ADD R5,R5,#4
		CMP R5,R3
		BLS LOOP2
		ADD R2,R2,#4
		CMP R2,R4
		BLS LOOP1
		LDR R2,=0X40000024
		MOV R0,R0,LSR #1
		MOV R4,#4
		MLA R3,R0,R4,R2
		LDR R1,[R3]
		MOV R0,#100
		END
```

### 3.编译

点击左上角编译按钮，开始编译程序，此时0错误，0警告，表示编译成功。
![请添加图片描述](https://img-blog.csdnimg.cn/a12a3181dca340109ef67c090b6c9823.jpeg)

### 4.stm32程序仿真调试

 (一)调试设置


1.点击魔法棒,点击 Debug 界面。选择 Use Simulator ，代表选择仿真器进行调试；同时勾选Run to main()防止程序不运行主函数而进入死循环（默认情况是勾选了的）在下方的`Dialog DLL`一栏中覆写`DARMSTM.DLL`，在一旁的`Patameter`中覆写`-pSTM32F103C8`，用于设置支持STM32F103C8的软硬件仿真。

![请添加图片描述](https://img-blog.csdnimg.cn/75086637b6e7448390338f7fe7dc14a3.jpeg)
<Br>


3.确定一下Port是JTAG，Reset可以设置为Autodetect或SYSRESEETREQ，然后点击OK返回上一级窗口，再点击OK。
![请添加图片描述](https://img-blog.csdnimg.cn/7b1f55ad1c7d4b9a9628c92741628b9f.jpeg)
<Br>

 (二)开始调试
1.选中带有红色d的放大镜开始调试，在②处就是仿真调试所需要的调试工具。![请添加图片描述](https://img-blog.csdnimg.cn/8ad9574aee1c4cacb4a1457b51614a9b.jpeg)
<Br>

2.在右下角窗口调出0x40000024地址，可以观察每个地址的变化
![在这里插入图片描述](https://img-blog.csdnimg.cn/a2a4948f44924d9b82e0dbafc8df33a4.png)
3.打断点
> 1.在给数组赋值完后，在后面打上断点，观察对应地址上是否赋值成功
>
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/bfe0a5ddb8a541929d4c9351a58a8006.png)

>		2.前面的过程进行冒泡排序，在把大小为中间值的R3寄存器中地址对应的值赋给R1寄存器
>
>	![在这里插入图片描述](https://img-blog.csdnimg.cn/e6770c15ac3646b8aa61b7506ceec1a5.png)

4.运行
![在这里插入图片描述](https://img-blog.csdnimg.cn/17ab91f133c94758b90f8f0de3ffce68.png)

> 1.点击运行一次

![在这里插入图片描述](https://img-blog.csdnimg.cn/ca0c7f7d5a614d7690e0f201eb1e75cb.png)
可以看到对应地址上已经赋值成功

>2.运行第二次

![在这里插入图片描述](https://img-blog.csdnimg.cn/846b850f10c141429bfca76ce76b7557.png)

到此为止，冒泡排序已经完成，我们看到数组里的数已经按顺序排在了地址里

>2.运行第三次
>
>![在这里插入图片描述](https://img-blog.csdnimg.cn/a2d5214a8d9f4097be342f10d158534c.png)
>这里看到R1寄存器里的值已经变为了数组中的中间值的大小，即实验成功

----

## 三.总结
对于接触keil时间不长的我来说，软件的很多操作都不熟悉，这次实验虽然简单，但是完成也花了很长的时间。包括汇编代码的完善，程序的调试以及找到对应的寄存器内容的变化。以前学过intel的汇编语言，这次用ARM的汇编语言有很多都不太了解，不过实验过后就熟悉了很多。