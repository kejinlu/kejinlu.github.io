---
layout: post
title: 能率燃气热水器智能化改造
categories:
- Homelab
tags:
- IoT
- noritz
- heater
- homeassistant
---

一般燃气热水器是很少需要操作的，设置好温度和水量之后基本长年都不需要调整设置，所以按理来说其智能化联网的需求并不是很迫切，不过能率的这些燃气热水器有一个问题就是每次断电之后再次通电，默认是关闭的状态，都需要手动按一下开关，非常麻烦。
所以需要一个断电恢复之后能够自动开机，以及能够实时监控燃气热水器的燃烧状态。这样也可以对燃气热水器的使用情况做一些分析或者异常检测。
我家用的是能率的室外机GQ-2040W，燃气热水器相比于传统电热水器或者空气能热水器这种储水式的还是方便不少，热水不限量，能够随时用随时加热。


<img width="50%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1665892207477-41f3e260-5b2f-4b63-af00-7db736f00e69.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_26%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_900%2Climit_0">

室外机通过两根线连接线控，有一些燃气热水器可以支持同时连接多个线控，我的这台经过测试只能连接一个线控，多个线控连接之后，时间长了线控会出现异常，影响燃气热水器的使用。

<img width="50%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1668845283871-8856a4c4-2af3-4114-8288-33da6fc109e1.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_34%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_900%2Climit_0%2Finterlace%2C1">

能率官方在国外推出了Wi-Fi版的控制器，NWC Adapter，不过国内没有销售，也不知道和国内销售的机型能不能适配，而且动辄四五百元人民币的价格也是相当的惊人，另外需要使用官方自己的App进行控制，无法接入Home Assistant系统。
所以我就寻思能不能对原装线控做一些DIY改造，读取相关的状态信息或者写入相关控制信号来达到目的。
虽然对数字电路一窍不通，但是心想多多少少有编程基础，数字电路相关的东西，不管是软件还是硬件，到了最底层都是0101，低电平高电平的序列，很多思想都是相通的。所以这次趁着国庆假期就着手对线控电路板做了一通研究，当然一开始并没有抱太大的希望。
## 一.前期准备

<img width="40%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1665893662722-585ca142-f97a-4656-8a94-1538915abe4c.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_23%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_800%2Climit_0">

数字电路逆向必须准备好万用电表，逻辑分析仪，杜邦线等必备的工具，这些工具不需要高端的，所有东西成本50元以内。
然后需要了解一些基本的电路知识，至少得知道电压，电阻（电阻，排阻），发光二极管，三极管这些电子元器件的基本性质。
我们要知道在数字电路中常见的电压有5V，3.3V； 电阻串联分压；发光二极管要注意正负极方向；三极管这个稍微复杂点，我们重点讲一下，三极管有NPN和PNP两种，不管哪种，都有三极：基极b，集电集c，发射极e。三极管有放大电流的作用（工作在放大区），也可以用作控制开关（一般工作在饱和区）。

NPN型三极管

<img width="20%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1664720562478-fe72cab2-0b0c-4ee8-93ea-ed30bad48fcf.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_14%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10">

NPN三极管中，当 Vb 比 Ve大0.7V左右的时候，b到e之间产生电流，然后c到e之间的电路也就通了，反之，电路就处于断开状态。电流为两进一出，bc都流向e。

PNP型三极管

<img width="20%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1665841668858-36c32058-bdac-494b-bd81-5c2941414f23.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_15%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10">

PNP三极管中，当 Vb 比 Ve小0.7V左右的时候，e到b之间就产生电流，然后e到c的电路也就通了，反之，电路就处于断开状态。电流为一进两出，e流向b和c。
总之我们可以通过配置基极和发射极的电压差，来控制集电极和发射极之间电路的通断。

## 二.线控逆向分析
### 2.1 几种方案
 对于燃气热水器控制器这种靠电力线来载波通讯的DIY改造的话一般有如下这些改造方案：

1. **PLC（**Power Line Communication，电力线通信不是可编程逻辑控制器**）方案**，这种方案也就是你得完全造出一个自己的线控，接上两根主机出来的电源线就可以控制了。这个方案是最难的，你需要对线载的模拟信号做彻底的逆向解析，工作量极大，近乎不可行。
2. **MCU数字信号拦截方案**，也就是在PLC芯片（这里是T6B70BFG）和MCU芯片之间进行桥接，截取数字信号获得状态信息，以及模拟发送控制信号给PLC芯片。
3. **LED，按钮末端方案**，这个就是分析LED电路的状态，获取当前状态信息，以及直接控制按钮的线路的通断模拟点击，这个方案简单，但是完整解析状态需要比较多的飞线。
4. **纯物理的方案**，使用智能机械按压设备来模拟人手点击按钮，以及通过摄像头或者感光器件来物理读取控制的状态。从效率上来讲，只有实在没有办法的情况下才会使用此种方案。

我比较偏向于第二种或者第三种方案。
不管怎么样，还是先对板子上的电路以及信号做一些必要的分析。

### 2.2 线控PCB板初识

首先在闲鱼上30块钱买了一个一样的二手线控，拆出板子进行分析研究。
拆看PCB板，我们可以发现其质量还是非常不错的，标识清晰，焊接做工都很好。
我们先看PCB正面，通过看各个元器件上的标识，查阅资料以及万用表进行检测，标记出下面的一些主要元器件
5v稳压器的作用主要是将热水器主机14V的输入电压转成5V的电压供各个芯片使用，T6B70BFG芯片主要通过两根电力线和主机通讯，MCU则接受T6B70BFG的数据更新LED显示以及按按钮之后发送指令给T6B70BFG。

<img width="60%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1665931695825-4e48065b-200e-49ba-96d7-55291e540d0d.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_79%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_940%2Climit_0">

PCB反面可以看到很多圆形的金属点，这种应该是用于PCB自动化测试的点，所以PCB上的主要节点都可以找到相应的测试点，这些测试点也给我们的线路DIY焊接带来了方便。以下图里标识出了几个重要的测试点。

<img width="60%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1665931232601-0ec2c177-984f-4506-975a-5b59cdff328d.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_83%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_967%2Climit_0">

### 2.3 T6B70BFG芯片
东芝的T6B70BFG，网上有详细的规格说明书，这款芯片主要用于热水器主机和控制器之间的接口电路，通过datasheet可以知道这个芯片大概的工作流程。
[datasheet.pdf](https://www.yuque.com/attachments/yuque/0/2022/pdf/328998/1664374286423-780da530-f0c7-4861-bfd4-e6b4c0895672.pdf?_lake_card=%7B%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2022%2Fpdf%2F328998%2F1664374286423-780da530-f0c7-4861-bfd4-e6b4c0895672.pdf%22%2C%22name%22%3A%22datasheet.pdf%22%2C%22size%22%3A403979%2C%22type%22%3A%22application%2Fpdf%22%2C%22ext%22%3A%22pdf%22%2C%22source%22%3A%22%22%2C%22status%22%3A%22done%22%2C%22mode%22%3A%22title%22%2C%22download%22%3Atrue%2C%22taskId%22%3A%22u6d1a5d03-7eac-4d58-ae95-42cd24e532b%22%2C%22taskType%22%3A%22upload%22%2C%22__spacing%22%3A%22both%22%2C%22id%22%3A%22u1d2947a6%22%2C%22margin%22%3A%7B%22top%22%3Atrue%2C%22bottom%22%3Atrue%7D%2C%22card%22%3A%22file%22%7D)
我们可以将关注点放在数字信号的 /DOUT端口，以及/SCTL控制信号端口，从“控制器”的角度来说，/DOUT为数字信号的输入，/SCTL为控制信号的输出。

<img width="25%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1664374680965-f7d3a6f1-dba5-4982-b2ef-cce3f3f26623.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_15%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10">

通过电路板可以看到T6B70BFG这两个端口都是通过一个10K的电阻连到了一颗名为SQE 702 K4C0的芯片（应该是一个定制芯片，查不到相关信息），然后PCB板子通过万用表分析这两个端口的连接。
将相关的电路简化了之后就是如下图的样子：

<img width="60%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1667053231283-ae8f18cd-0a13-4a36-b812-3e06f2fa5f52.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_23%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10">

### 2.4 MCU数据协议分析
我们先了解下线控的基本功能， 首先就是开关功能，燃气热水器每次断电后再通电，默认是关闭状态的，需要按下控制器的开关开启；然后还可以设置水温，水温的值有37,38,39,40,41,42,43,44,45,46,47,48,60,75。按下水量按钮，可以设置水量，水量有4,6,8,10,12,14,16,18,20,22,24,26,30,35,40,99。 这里可以看到温度60，75度是非常高的温度了，最好能限制，不能将其纳入远程调节的范围，避免安全问题。
 
逻辑分析仪如下接法，然后通过Logic2软件进行抓包数据，然后进行线控的开机关机操作。

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1667053231283-ae8f18cd-0a13-4a36-b812-3e06f2fa5f52.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_23%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />

通过Logic2可以看到，没次按下按钮操作时候，/STL 端 Channel0会收到一串数据信号序列，然后紧接着/DOUT会输出一段和/STL 端接收到的一样的数字序列，然后大概27ms之后 又会收到一串数字信号序列。
/DOUT端收到一个和发出去的命令序列一样的序列估计是因为此T6B70BFG是单线接口通信，所以模拟信号发出的时候，T6B70BFG自身也能收到这个信号，所以会在/DOUT端进行重放，内部MCU处理的时候应该是忽略了此信号，而只处理后继收到的那个信号序列。

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1664780861015-b8f3d33c-059b-4017-bbd1-03a1eb3104b4.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_57%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1500%2Climit_0" />

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1664782012208-b629d97d-d505-48f1-98e1-60fb44ac22f7.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_34%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />

这边对各种状态下的数字信号做了抓取分析，虽然有一些状态值可以看出规律来，但是像温度这种数值 没有分析出规律，真想做的话只能一一对应的进行枚举分析了，如果真要通过MCU协议的方式来实现，那么只能一一对应的数据来实现了，代码会非常繁复。
或许后面有机会再继续研究，最终破解出其数据协议。
所以我们继续看末端交互的电路，主要是LED显示和按钮控制。


### 2.5 LED电路分析
我们再来看看线控板的LED实现的细节，如果每一个发光二极管都通过一个电平信号来控制，那么一共17个发光二极管就一共需要18个引脚（一根共用引脚，17根独立的控制引脚），非常浪费。
所以一般实现的时候都是将发光二极管分组，每一组中的LED进行编号，同一分组的所有led公用引脚接分组控制端，不同组的相同编号的LED共用控制引脚。分时轮流显示时，也就是一个微小的时间片段中只有某一组LED能够显示，然后控制引脚此时只控制当前分组的各个led的状态，由于时间片段非常短，循环交替显示各个分组的LED，对于人眼来说，各个分组的LED都得到了显示。 其实现代LED显示器本质上也是这么个原理，所以用手机拍视频或者拍照的时候有时候能看出闪烁。
通过万用电表的分析，这边对LED显示电路做了近乎1比1的还原，具体的电路图可见 [https://oshwhub.com/kejinlu/noritz-controller](https://oshwhub.com/kejinlu/noritz-controller)，通过嘉立创EDA可以进行仿真模拟，修改各个控制引脚的电平来控制验证LED的显示。

<img width="80%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1665932977386-57a4b9fb-dfbc-498d-861b-e0ba760f0900.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_66%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />

通过LED电路的分析，我们可以知道 这颗SQE 702 K4C0的芯片其作用就是接受燃气热水状态的数字信号，然后通过LED显示出来；以及接受按钮的操作信号，输出控制数字命令。


### 2.6 开关电路
按钮的实现比LED电路简单多了，基本都是给某个MCU芯片上的引脚通上低电平然后断开就实现了一次触发。
这里以开关机按钮为例，电路示意图如下：

<img width="30%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1665891278109-4ad98e68-a0ae-41d2-9cb5-a65af2917340.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_17%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />

开关按钮按下松开，也就是开关闭合后断开就完成一次开关机指令的触发。


## 三.末端数字信号方案实现
方案目标：简单，无损
简单不仅仅是电路改造简单，而且固件代码要简单，稳定且很容易理解。
无损就是不切断修改原有燃气热水器线控的电路通路，不改变原有数字信号序列，不影响原有线控的所有功能和稳定性。

### 3.1 开关机及燃烧状态
未开机

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1665642575082-a3d9b20f-d5b7-4d88-b47f-132205830c84.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_44%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />

开机状态

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1665642830392-8477fccd-137b-4d7a-a007-4cee3cb611d5.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_41%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />

燃烧状态

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1665642873982-35f1a60c-3d1f-4ff2-882f-7fdb8302758f.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_39%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />


我们的目标是能够判断当前燃气热水的开机状态，燃烧状态，延迟控制在1s内，通过上面的逻辑信号序列我们可以将问题简化，就是直接通过分析开机/燃烧LED信号这一个信号的状态就可以判断目前燃气热水器的状态。分组信号任何时候就是对LED分组进行轮询。

### 3.2 电路设计及实现
[https://oshwhub.com/kejinlu/noritz-controller](https://oshwhub.com/kejinlu/noritz-controller) 

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1667562522079-13de3709-64e5-4311-afca-b8dc20a120bd.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_36%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />

这里我使用的是ESP32-DevKitC 如果你有别的基于ESP32的或者ESP8266的也应该都可以。
关于此电路设计有几个注意点：

- 约定
   - 我们将智能控制器数据读入端叫做 RX（Receive）
   - 信号输出端叫做TX（Transport），这里TX其实并是不普通意义上的信息输出，只是控制和GND的通断
- ESP32-DevKitC 开发板
   - 必须和线控共地，也就是线控板的GND需要接到ESP32的GND端。
   -  供电，开发版使用USB单独供电，经过测试如果从线控板的5v供电的话会影响线控的正常运行。
   - GPIO34，35引脚是input only的，所以 输出TX使用了下面的32引脚。
- 用作TX开关控制的三极管
   - 基极输入端注意设置下拉电阻R3，这里的下拉电阻既可以保证当GPIO32断开或者ESP32断电时候能够有确定的低电平，也可以在GPIO 32 输出高电平的时候进行分压，控制输入电压。
   - 这里的电阻大小都是基于能够让三极管工作在饱和区；且电流足够小。这些都需要通过计算而得，电阻大小并不是唯一，只要满足条件即可。
- 开关、燃烧LED状态读取端RX
   - 看之前的LED的仿真电路我们可以知道，RX端连接点其实也是和开关、燃烧LED直接相连的，所以我们的智能控制器其实是和线控MCU是一个并联的关系。
   - 当RX是高电平的时候，线控MCU的高电平是5V，所以为了安全做了一个电阻降压，可以选择两个R1，R2比值接近2：1的两个电阻。这样5V降压之后就接近3.3V了。 
   - 当RX是高电平的时候，相关LED都应该是不亮的，不过这边接入了我们自己的电路之后有一个问题，如果R1，R2过小，并联之后就会有电流从LED中流过，导致 LED异常亮起。所以我们需要R1，R2的阻值比较大，使得电流极小，不足以使LED点亮。


然后我们就按照设计好的电路图，在线控PCB相应的测试点上引出信号线。

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1666014029285-5652d40a-33b3-4256-a12c-23fb8c3eecd2.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_25%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_860%2Climit_0" />

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1666195441996-56dc35fe-237d-4c56-b44b-b8ff4ba314eb.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_171%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1920%2Climit_0" />

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1666192310669-272b800e-0bc1-48a6-bfb5-3910e0486af0.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_21%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_746%2Climit_0" />

### 3.3 软件编码
[https://github.com/kejinlu/noritz-controller](https://github.com/kejinlu/noritz-controller) 
详细代码见github
### 3.4 Homeassistant接入
这一步算是最简单的了，在configuration.yaml中进行配置即可，然后再将其加入卡片

{% raw %}
```yaml
mqtt:
  switch:
    - name: "Noritz Power"
      unique_id: "switch.noritz_power"
      state_topic: "noritz/state"
      command_topic: "noritz/cmd"
      payload_on: '{"power":"ON"}'
      payload_off: '{"power":"OFF"}'
      state_on: "ON"
      state_off: "OFF"
      retain: false
      value_template: >
        {% if value_json.power is defined %}
          {{value_json.power}}
        {% endif %}

  binary_sensor:
    - name: "Noritz Heating"
      unique_id: "binary_sensor.noritz_heating"
      state_topic: "noritz/state"
      payload_on: "ON"
      payload_off: "OFF"
      qos: 1
      value_template: >
        {% if value_json.heating is defined %}
          {{value_json.heating}}
        {% endif %}
```
{% endraw %}

<img width="60%"  loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1667122682421-c7351a3d-2741-4751-b573-e7fe6d519ef7.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_29%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10" />

最后使用的实际情况请看下面的视频:
[点击查看【bilibili】](https://player.bilibili.com/player.html?bvid=BV1p84y1B7GJ)
