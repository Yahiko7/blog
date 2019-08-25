### GPU VS CPU ?
#### 什么是CPU?
CPU是中央处理器（CPU，central processing unit）的缩写，通常被称为计算机的“大脑”。它是数百万个晶体管的集合，可以通过操作来执行各种各样的计算。是电脑的核心配件，其功能主要是解释计算机指令以及处理计算机软件中的数据。CPU是计算机中负责读取指令，对指令译码并执行指令的核心部件。
#### 什么是GPU?
GPU即图形处理器（英语：Graphics Processing Unit，缩写：GPU），是一种专门在个人电脑、工作站、游戏机和一些移动设备（如平板电脑、智能手机等）上图像运算工作的微处理器。需要注意的是，GPU与我们常说的显卡并不等同，GPU是显卡的核心,显卡，则是由GPU、显存、电路板，还有BIOS固件组成的。
#### CPU和GPU有什么区别?
从外形上看，CPU和GPU都有点相识，它们都是由数亿个晶体管组成的，每秒可以处理数千次运算。从功能上看，CPU可以做的事情更多，CPU就像是电脑的大脑一样，而GPU更加适合做专一重复简单的事情，也就是苦力工。GPU的特点是包含了大量的计算单元，这使得它可以同时进行大量的计算，这一点在图像方面的处理是非常有用的，GPU可以使用数百个核心对数千个像素进行时间敏感的计算，使复杂的3D图形显示成为可能。

![image](https://blogs.nvidia.com/wp-content/uploads/2009/12/6a00d834515fca69e201287663224d970c-320pi.jpg)

#### 什么是GPU加速？
GPU 加速能將程序中运算密集的工作转移从CPU转移到GPU中去，而每一个GPU含有数千个细小而高效的核心，专门用作同时处理多重任务，从而加速程序的执行速度。


### GPU加速在前端中的应用
#### GPU在CSS中的应用
在以下情况可以触发GPU加速
- CSS3过渡
- CSS3 3D变换
- 画布绘图
- WebGL 3D绘图

对于大多数HTML5应用程序，可能没有使用到以上几种。但是我们仍然可以使用某些方法让浏览器开启GPU加速
```
.cube {
   -webkit-transform：translate3d（0,0,0）;
   -moz-transform：translate3d（0,0,0）;
   -ms-transform：translate3d（0,0,0）;
   transform：translate3d（0,0,0）;
  / *其他变换属性在这里* /
}
```

参考文章：

[Increase Your Site’s Performance with Hardware-Accelerated CSS](https://blog.teamtreehouse.com/increase-your-sites-performance-with-hardware-accelerated-css)

[Improving HTML5 app performance with GPU accelerated CSS transitions](https://www.urbaninsight.com/article/improving-html5-app-performance-gpu-accelerated-css-transitions)


