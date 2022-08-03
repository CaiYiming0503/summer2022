﻿
 该产品是一款简易物联网温湿度传感系统，利用ESP8266-01S传感器连接onenet云平台，数据可在OLED上显示出来，并且可以在云端看到。该系统可以用于智能家居中。这里我运用到的硬件部分有:HPM6750MINI开发板，AHT10温湿度传感器，ESP8266WIFI检测传感器，SSD1306显示传感器。需要特别注意的点，在这里我们使用的是软件模拟I2C,我们要找对相应的引脚进行连接。下面是对各个传感器的相关说明，以及我写程序时遇见的错误和解决方案。
- 整体框架如图所示：
- ![[1659454006914.png]]
-
一定要接线正确！![[Pasted image 20220802232822.png]]
-
- [[Pasted image 20220802213032.png]]
- TX和RX是互连的，不要忘记。![[1659447099340.png]]
    ![[49a8563897070d62306d21816ee1129.png]]
- ![[df2a516cc1bf2bbb14f15ac626aac9f.png]]
- 在软件部分：我运用到的软件包是如下部分，并且对其进行了相应的配置。  
    ![[1659447341908.png]]
-   因为AHT10对接了SENSOR框架，我们可以在AHT10的README中找到相应硬件初始化的函数，把他引用到我们的函数中即可。  
    
-   ![](https://api2.mubu.com/v3/document_image/468eb272-fd9f-4a16-a29e-73d7caf329ca-16414564.jpg)  
    
-   下一步我们进行如下操作就可以进行传感的访问了。对应的代码如下所示。这里需要注意的是，我们的查找和打开只需要进行一次就可以，所以说不需要写进while循环里。因为在主函数里我把温湿度定义为了全局变量，所以在线程的入口函数里面，就直接进行赋值了。![](https://api2.mubu.com/v3/document_image/20e655e9-a7c1-4bd7-b7f4-745d50b69026-16414564.jpg)![](https://api2.mubu.com/v3/document_image/8fec9545-8665-4d8e-8770-8e0125dcc3ad-16414564.jpg)![[Pasted image 20220802210508.png]]
- 这边的data是传感器数据结构体，data.data.temp中第一个data代表着这是rt_sensor_data结构体变量，第二个data代表的是sensor_data结构体中data这个成员![[504d65f4a85ef938b9b661dfe6fb6cd.png]]
- AHT10还有一个问题就是我们要把是使能彩色日志打开，不然的话终端是没有显示的。
- ![[1659446880123.png]]
- 第二个就是ESP8266模块，在这个模块中呢，如果遇见了以下问题
- ![[6ee4c62ad076ac368a45f8c9a0c0e41.png]]
- 说明是需要进行固件![[b49216dfbccd2bbe23ce10b3f6677f2.png]]的更新，固件更新的软件要用最新版本。在这个过程中是非常磨人的，可能会出现各种各样的错误会下载不成功，我们就只能静下心来，耐着性子一步一步去进行这个操作。会用到USB转TTL串口。还有就是WIFI的名称和密码一定要输入对。WiFi的名称不能够有特殊字符。
- 第三个是OLED显示模块，在这个模块中
- ![[aac84cb24d992254e0174544ba89d38.png]]
- 这个是清理缓存的函数
- ![[0c3cf91d9f73a83b54940c3387c2768.png]]
- 这个是设置字体的函数，里面对应2个参数：一是刚刚初始化的句柄，二是字体对应宏
- ![[b5e6768c241ec0b9c3d6bce248f9da9.png]]
- 一句柄，二三xy轴，四要显示的字符串。
- ![[1659448147492.png]]
- 调用Sprintf函数把AHT10的温度数据格式化到字符串数组变量strTemp中，然后我们还得定义一个字符串去存转出来的字符串
- ![[1659448996668.png]]
外头只放只需要运行一次的初始化和局部变量声明，线程里的结构就和裸机开发时候main函数是一样的，一开始声明变量和初始化，线程切换的时候就和裸机主函数被中断是一样，在哪里被中断回来时还是从哪里开始运行，切换回来不会从线程入口函数一开始运行。
出现这个问题
![[]]![[7e846e06fa5dbd8ce4fd71659d12557 1.png]]
解决办法：在函数的开头加上延时，并且在我们的两次发送之间也要加上延时函数。
连上云平台后 ，我们怎么能让他显示小数呢？这里我们运用到的是强制转换，
![[1659449681955.png]]
![[fb557106d43cd051b3acd9b437a5397.png]]
并且我们要把这个进行勾选。