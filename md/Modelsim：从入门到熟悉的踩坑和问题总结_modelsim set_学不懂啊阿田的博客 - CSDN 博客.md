> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/mmphhh/article/details/111315483?app_version=6.1.6&code=app_1562916241&csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%22111315483%22%2C%22source%22%3A%22qq_39636216%22%7D&uLinkId=usr1mkqgl919blen&utm_source=app)

#### 文章目录

*   [前言](#_5)
*   [一、窗口信号路径隐藏](#_11)
*   [二、界面说明](#_22)
*   [三、波形窗口的快捷键](#_28)
*   [四、主窗口和源代码窗口的快捷键](#_41)
*   [五、波形窗口颜色设置](#_48)
*   [六、设置显示时间单位](#_68)
*   [七、信号与信号值查找](#_76)
*   [八、Modelsim 打开错误](#Modelsim_88)
*   [九、自动仿真环境搭建](#_98)
*   [十、参考文献](#_170)
*   [十一、时间记录](#_175)

前言
--

一些 modelsim 的使用问题，这些问题是学习和熟悉 Modelsim 环境的重大障碍，有时在开发过程中，处理这些问题会浪费大量时间，同时提升开发周期。故将这些问题补充与本文中，方便查找。很多也是我个人的踩坑记录。

一、窗口信号路径隐藏
----------

可以通过以下步骤设置，默认是没有隐藏的：

*   点击 Tools — Window preference。
*   将 display signal path 的数值改写成 1，即可完成设置。  
    ![](https://img-blog.csdnimg.cn/2020121714592649.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)

另外一种方法：点击该页面左下角的灰色原点即可隐藏路径名。但该法不能实现一劳永逸的目的，每次打开想隐藏都需要点下。  
![](https://img-blog.csdnimg.cn/20201217150119894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)

二、界面说明
------

![](https://img-blog.csdnimg.cn/20201217150157464.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)  
最好每次仿真启动后，都重置下，不然起始时间段可能没有信号显示。

![](https://img-blog.csdnimg.cn/20201217150236266.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)

三、波形窗口的快捷键
----------

关于鼠标：

*   Ctrl + 鼠标左键 + 从左上向右下拖拉：放大（选中区域）
*   Ctrl + 鼠标左键 + 从左下向右上拖拉：缩小（基本全局）
*   Ctrl + 鼠标左键 + 从右下向左上拖拉：缩放至满屏
*   鼠标左键拖拉：可直接移动最近的光标
*   Ctrl + 鼠标左键 + 在滚动条上点击：滚动至顶部或底部（竖直滚动条上）、左端或右端（水平滚动条上）

关于键盘：

*   C/c：当前光标居中并放大
*   I/i/+：(Zoom in) 放大
*   O/o/-：(Zoom out) 缩小
*   F/f：(Zoom full) 缩放至满屏
*   L/l：(Zoom last) 缩放为上次
*   R/r：(Zoom range) 缩放范围
*   方向键：上下左右滚动（以行为单位）
*   翻页键：上下滚动（以页为单位）
*   Ctrl + 左右方向键：左右滚动（以页为单位）
*   Tab：向前（右）查找下一个跳变沿（对应着选中的信号）
*   Shift + Tab：向后（左）查找下一个跳变沿
*   Ctrl + f：打开查找对话框

四、主窗口和源代码窗口的快捷键
---------------

很多快捷键，也可以使用 Windows 中的快捷键操作，就不列出来了，一些列出一些可能用到的：

*   Ctrl + f：查找
*   F3：查找下一个
*   F9：运行仿真
*   F10：继续执行仿真
*   F11：单步执行（进入）
*   F12：单步执行（跳过）

五、波形窗口颜色设置
----------

Modelsim 波形默认在黑色背景中显示绿色波形。看起来很吃力，为且制作文档时的，这种颜色打印效果并不好。为此，参考网上教程设置了下，结果非常的好。

*   点击【Tools】->【Edit Preferences】菜单打开配置窗口。  
    ![](https://img-blog.csdnimg.cn/20201217150747574.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)  
    在左侧的 Window List 中选中 Wave Windows 选项，然后在中间的 Wave Windows Color Scheme 中选择一个需要修改颜色的位置，然后点击右侧的调色板中的对应颜色即可。  
    ![](https://img-blog.csdnimg.cn/20201217150819227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)  
    设置波形背景窗口为白色：waveBackground -> 白色  
    设置左侧信号名称背景窗口为白色：Background -> 白色  
    设置左侧信号名称文字颜色为黑色：foreground -> 黑色  
    设置时间指示颜色为黑色：timeColor–> 黑色  
    设置向量（多位宽信号）波形颜色为黑色：vectorColor -> 黑色  
    设置所有逻辑波形（单 bit 信号）颜色为黑色：LOGIC_0 和 LOGIC_0 -> 黑色  
    设置波形中显示的文字颜色为黑色：textColor -> 黑色  
    设置竖格颜色为灰色（GRAY70）：gridColoR –> 灰色  
    设置左侧信号栏信号被选中时的框颜色：selectBackground –> 红色  
    设置左侧信号栏信号被选中时的字体颜色：selectForeground –> 黑色  
    设置移动轴的颜色：cursorColor –> 红色

下图为本人配置好的波形窗口，看着还是很舒服的：  
![](https://img-blog.csdnimg.cn/20201217150909132.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)

六、设置显示时间单位
----------

在波形窗口的时间单位可以进行更改：

*   点击 tools——>window preference。
*   点击 grid & timeline 选项卡，在 Time unirs 选项卡处选择。
*   Time units 可选的有：fs、ps、ns、us、ms、sec、min 和 hr。

![](https://img-blog.csdnimg.cn/20201217151037414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)

七、信号与信号值查找
----------

关于信号查找：（适合仿真信号很多时用）

*   先在左侧信号栏处选中一个信号
*   按下 CTRL + F，即可输入待找的信号

![](https://img-blog.csdnimg.cn/20201217151207383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)

关于信号的查找：

*   点击某信号，可以选中
*   在 search 框中输入待找信号值，注意和显示进制相对应
*   按下右边的查找键，可找到该信号的搜索值

![](https://img-blog.csdnimg.cn/20201217151312733.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)

八、Modelsim 打开错误
---------------

出现错误：Unable to checkout a viewer license necessary for use of the ModelSim graphical user interface.Vsim is closing。关闭后闪退。

出错原因：安装 Modelsim 后，复制了 patch，且生成了 LICENSE.TXT 文件，但是没有配置环境变量。

解决：

*   把 MentorKG.exe、mgls.dll 和 patch_dll.bat（或 patch64_dll.bat）一起拷贝到 modelsim 安装目录的 win32（或 win64）下。
*   打开环境变量，在系统环境变量一栏处，设置环境变量 MGLS_LICENSE_FILE，变量值为 license 放置的目录。
*   右键快捷方式，以管理员身份运行，重新启动后，就没有这个错误了。  
    ![](https://img-blog.csdnimg.cn/20201217151804208.png)

九、自动仿真环境搭建
----------

使用 bat 批量命令处理文件，可快速启动仿真软件，此处对仿真环境进行配置。

```
% run_simulation.bat文件 %
@echo off
@cls
title FPGA Auto Simulation batch script

echo ModelSim simulation
echo.
echo Press '1' to start simulation
echo.

:input
set INPUT=
set /P INPUT=Type test number: %=%
if "%INPUT%"=="1" goto run1
goto end

:run1
@cls
echo Start Simulation;
echo.
echo.
cd testbench
vsim -do "do compile.do"
goto clean_workspace

:clean_workspace

rmdir /S /Q work
del vsim.wlf
del transcript.

:end

```

```
# compile.do文件

vlib work
vmap work work

#library 之前添加库时已经编译，可不用再编译
#vlog  -work work ../../library/artix7/*.v

#IP  相应的IP需要指定
#vlog  -work work ../../../source_code/ROM_IP/rom_controller.v

#SourceCode 对源码编译
vlog  -work work ../design/vlg_design.v

#Testbench 对测试脚本编译
vlog  -work work testbench_top.v 

#把testbeach.top添加到work中
vsim -voptargs=+acc work.testbench_top

#Add signal into wave window
do wave.do

#run -all


```

一些测试：  
![](https://img-blog.csdnimg.cn/2020121715193744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)  
![](https://img-blog.csdnimg.cn/20201217151946697.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)  
点击 run -all 开始仿真，可以把脚本中相应命令去掉，也能直接进入仿真了。  
![](https://img-blog.csdnimg.cn/2020121715204779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21tcGhoaA==,size_16,color_FFFFFF,t_70)

十、参考文献
------

*   [Modelsim——工程建立和常用设置](https://www.cnblogs.com/xianyufpga/p/10905203.html)
*   [modelsim 快捷键](https://blog.csdn.net/mshgocn/article/details/74451734)
*   **[小梅哥 FPGA：Modelsim 修改波形显示颜色](http://www.corecourse.cn/forum.php?mod=viewthread&tid=28356)**
*   **[modelsim 波形设置显示时间单位](https://blog.csdn.net/dashixiong97/article/details/103613772)**
*   特权同学：modelsim 自动仿真环境搭建

十一、时间记录
-------

*   2020 年 08 月 02 日：窗口信号路径隐藏、界面说明、波形窗口的快捷键、主窗口和源代码窗口的快捷键。
*   2020 年 08 月 03 日：波形窗口颜色设置、设置显示时间单位
*   2020 年 08 月 06 日：信号与信号值查找
*   2020 年 09 月 05 日：解决 Modelsim 打开错误的问题
*   2020 年 09 月 25 日：自动仿真环境搭建