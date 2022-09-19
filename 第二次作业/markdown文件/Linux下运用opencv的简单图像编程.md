

@[toc]

----

# Linux下运用opencv的简单图像编程
<br>

## 一.编写一个打开图片进行特效显示的代码
<Br>

###   (一) . 用普通方式编译程序
#### 1.准备工作：
> - 在opencv文件夹里创建工作文件夹以及一个cpp文件
```S
	cd opencv-3.4.16
```

```S
	mkdir mytest
```

```S
	cd mytest
```

```S
	touch test.cpp
	
	vim test.cpp
```
	test.cpp代码：

```c
#include <opencv2/highgui.hpp>
#include <opencv2/opencv.hpp>
using namespace cv;
using namespace std;
int main(int argc, char** argv)
{
	CvPoint center;
    double scale = -3; 

	IplImage* image = cvLoadImage("lena.jpg");
	argc == 2? cvLoadImage(argv[1]) : 0;
	
	cvShowImage("Image", image);
	
	
	if (!image) return -1; 	center = cvPoint(image->width / 2, image->height / 2);
	for (int i = 0;i<image->height;i++)
		for (int j = 0;j<image->width;j++) {
			double dx = (double)(j - center.x) / center.x;
			double dy = (double)(i - center.y) / center.y;
			double weight = exp((dx*dx + dy*dy)*scale);
			uchar* ptr = &CV_IMAGE_ELEM(image, uchar, i, j * 3);
			ptr[0] = cvRound(ptr[0] * weight);
			ptr[1] = cvRound(ptr[1] * weight);
			ptr[2] = cvRound(ptr[2] * weight);
		}

	Mat src;Mat dst;
	src = cvarrToMat(image);
	cv::imwrite("test.png", src);

    cvNamedWindow("test",1);  	imshow("test", src);
	 cvWaitKey();
	 return 0;
}
	
```
<br>

#### 2.准备一张图片，移到相同目录下


![在这里插入图片描述](https://img-blog.csdnimg.cn/478408d609774fe7af7aeda78ebe6051.png)
***注意：把图片名改为lena.jpg***
<br>
<br>

#### 3.编译程序
> **c++文件编译指令：**
> 
 ***若为c源程序的话就把g++换成gcc***
```S
	g++ test.cpp -o test `pkg-config --cflags --libs opencv`
```

> 指令解释:
> gcc编译器：gcc + 文件名 + -o + 输出文件流名称 +`支持包`

![在这里插入图片描述](https://img-blog.csdnimg.cn/2ca7d5398a31476e848f93f0435cc51c.png)

#### 4.运行程序
```S
	./test
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6aeaeecaa6d649c79e4b7b8847d71937.png)
可以看到生成了另一个test.png图像，生成的图像周围有暗角，与原图像有很大不同
![请添加图片描述](https://img-blog.csdnimg.cn/5802c1204e594833be9b1b030068933c.png)
<br>

###   (二) . 用make+makefile编译程序（用变量命名格式写makefile文件，并包括 clean选项)
<br>

#### 1.在mytest文件夹再创建一个Makefile文件，并键入指令
```s
	vim Makefile
```

```S
test:test.o
	g++ -o test test.o `pkg-config --cflags --libs opencv`
test.o:test.cpp
	g++ -c -o test.o test.cpp
clean:
	g++ -f test test.o	
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9c922fc3de3c4a93884666710f73ca7f.png)
#### 2.make指令运行Makefile文件
```S
	make
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/3ce98a1108a540e99116a580b26acfa5.png)![在这里插入图片描述](https://img-blog.csdnimg.cn/98e3e51b92b94a75aae0fe40c1718c26.png)
<br>

#### 3.运行程序
![在这里插入图片描述](https://img-blog.csdnimg.cn/52ebae5e712f4f1a9275660c5a54178c.png)
<br>

## 二.编写练习使用opencv库编写打开摄像头压缩视频的程序
<br>

###   (一) . 打开已有视频播放
<br>

#### 1.准备工作，在opencv文件夹里创建工作文件夹以及一个cpp文件
```S
	cd opencv-3.4.16
```

```S
	mkdir test1
```

```S
	cd test1
```

```S
	touch test2.cpp
	
	vim test2.cpp
```
><font color="#dd0000">在编译之前把对应视频文件移入test1文件夹中</font>

	test2.cpp代码：

```c
#include <opencv2/opencv.hpp>
using namespace cv;
int main()
{
	//从摄像头读取视频
	VideoCapture capture("视频文件名");
	//循环显示每一帧
	while(1){
		Mat frame;//定义一个Mat变量，用于存储每一帧的图像
		capture >> frame;//读取当前帧
		imshow("读取视频帧",frame);//显示当前帧
		waitKey(30);//延时30ms
	}
	system("pause");
	return 0;
}

```

>注意：
>> - 第14行代码运用<font color="#dd0000">waitKey</font>代码是因为值调用**imshow**()获取视频帧而不调用**waitKey**来流出时间跟踪绘制的话则imshow函数则**停止工作**，屏幕上**不会出现任何内容**





#### 2.编译文件
> **c++文件编译指令：**
> 
 ***若为c源程序的话就把g++换成gcc***
```S
	g++ test2.cpp -o test2 `pkg-config --cflags --libs opencv`
```

> 指令解释:
> gcc编译器：gcc + 文件名 + -o + 输出文件流名称 +`支持包`

![在这里插入图片描述](https://img-blog.csdnimg.cn/bbbb5352b8bb40bb9b06096145969086.png)
#### 3.运行文件
```S
	./test2
```
![请添加图片描述](https://img-blog.csdnimg.cn/e4a80016612c4ab8b3242b4e270116c9.png)



> 此时运行成功，对应视频文件被打开，但是我们发现此时无法关闭视频，在点击右上角关闭后会不断弹出。此时我们应该在**while**循环最下面加一步getWindowProperty的返回值判断。当我们没点击关闭按钮时，**getWindowProperty**返回值为 **1**。所以只需判断返回值为**不为1**即可。（实际上点击关闭按钮后返回值为 **0** ）
```C++
 if( getWindowProperty("读取视频帧",WND_PROP_AUTOSIZE) != 1)
 
 break;
```

***完整代码：***
```c
#include <opencv2/opencv.hpp>
using namespace cv;
int main()
{
	//从摄像头读取视频
	VideoCapture capture("man.mp4");
	//循环显示每一帧
	int key;//记录键盘按键
	while(1){
		Mat frame;//定义一个Mat变量，用于存储每一帧的图像
		capture >> frame;//读取当前帧
		if(frame.empty())//播放完毕，退出
			break;
		imshow("读取视频帧",frame);//显示当前帧
		waitKey(30);//掩饰30ms
		if( getWindowProperty("读取视频帧",WND_PROP_AUTOSIZE) != 1)
            	break;
	}
	system("pause");
	return 0;
}

```

###   (二) . 打开摄像头进行录制并保存
#### 1.虚拟机获取摄像头权限
> 使用快捷键Win+R，输入services.msc ，并回车。

![在这里插入图片描述](https://img-blog.csdnimg.cn/a2986b741f584d7b89ff0f5285979db9.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/1e67e80d75b14783b404d0faf2714a2c.png)
>确保USB服务状态为正在运行 

> 回到VMware
![在这里插入图片描述](https://img-blog.csdnimg.cn/c1088cd56e594eb487374b818413343c.png)
> 选择 “ 虚拟机 ” ，再选择 “ 可移动设备 ” ，再选择 “IMC Networks USB2.0 VGA UVC WebCam ” ，最后点击 “ 连接 ” ，再弹出的窗口内点击 “ 确定 ” 。


![在这里插入图片描述](https://img-blog.csdnimg.cn/b007be233f5e420a981df062e6fef68f.png)
<br>
<br>

#### 2.准备，编译以及运行
**由于创建文件步骤以及编译运行代码相同，不再赘述，只贴出对应cpp源码**
```c
/*********************************************************************
打开电脑摄像头，空格控制视频录制，ESC退出并保存视频RecordVideo.avi
*********************************************************************/
#include<iostream>
#include <opencv2/opencv.hpp>
#include<opencv2/core/core.hpp>
#include<opencv2/highgui/highgui.hpp>
using namespace cv;
using namespace std;

int main()
{
	//打开电脑摄像头
	VideoCapture cap(0);
	if (!cap.isOpened())
	{
		cout << "error" << endl;
		waitKey(0);
		return 0;
	}

	//获得cap的分辨率
	int w = static_cast<int>(cap.get(CV_CAP_PROP_FRAME_WIDTH));
	int h = static_cast<int>(cap.get(CV_CAP_PROP_FRAME_HEIGHT));
	Size videoSize(w, h);
	VideoWriter writer("RecordVideo.avi", CV_FOURCC('M', 'J', 'P', 'G'), 25, videoSize);
	
	Mat frame;
	int key;//记录键盘按键
	char startOrStop = 1;//0  开始录制视频； 1 结束录制视频
	char flag = 0;//正在录制标志 0-不在录制； 1-正在录制
	
	int x;

	while (1)
	{
		cap >> frame;
		key = waitKey(100);
		if (key == 32)//按下空格开始录制、暂停录制   可以来回切换
		{
			startOrStop = 1 - startOrStop;
			if (startOrStop == 0)
			{
				flag = 1;
			}
		}
		if (key == 27)//按下ESC退出整个程序，保存视频文件到磁盘
		{
			break;
		}

		if (startOrStop == 0 && flag==1)
		{
			writer << frame;
			cout << "recording" << endl;
		}
		else if (startOrStop == 1)
		{
			flag = 0;
			cout << "end recording" << endl;
			
		}
		imshow("picture", frame);
		
            	
	}
	cap.release();
	writer.release();
	destroyAllWindows();
	return 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/15f12912018c45ba85ce60fd89f7dba3.png)
> 点击空格键开始录制，录制之前控制台一直输出 end recording，空格之后输出recording。录制结束后在目录生成录制的视频文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/796967ee77dd448284397b4469dcdd3c.png)
## 三.总结
**这次是第一次接触到opencv的安装以及运用，其实几个任务的难度并不高，但是安装opencv的过程比较繁琐，容易出现一些意外。还有在寻找怎样才能让打开的视频能够顺利一次性关闭上也花了不少功夫，也参考了很多资料的内容。不过最后还是做成功了，收获还是很多的。**


## 四.参考资料
https://blog.csdn.net/weixin_46129506/article/details/120646081
https://blog.csdn.net/ssj925319/article/details/109231145
https://mooc1.chaoxing.com/ueditorupload/read?objectId=2b8aea8003e93aaf6326bdba9a606cae&fileOriName=%25E7%25AC%25AC4%25E5%2591%25A8%25E5%25B5%258C%25E5%2585%25A5%25E5%25BC%258F%25E7%25B3%25BB%25E7%25BB%259F%25E4%25BD%259C%25E4%25B8%259A-opencv%25E7%25BC%2596%25E7%25A8%258B%25E5%258F%2582%25E8%2580%2583%25E8%25B5%2584%25E6%2596%2599.docx
