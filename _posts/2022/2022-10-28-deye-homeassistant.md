---
layout: post
title: 德业除湿机接入Homeassistant
categories:
- Homelab
tags:
- IoT
- homeassistant
---

之前已经有人对德业除湿机做过反编译，找到了其背后的实现原理，并通过修改homeassistant相关组件远吗接入homeassistant,[https://xiking.win/2020/11/12/3-deye-dehumidifer-add-to-homeassistant/](https://xiking.win/2020/11/12/3-deye-dehumidifer-add-to-homeassistant/) ，也有将其做成Homekit插件的[https://github.com/IcesandSora/homebridge-deye](https://github.com/IcesandSora/homebridge-deye) 。我是想将其接入Homeassistant，但是并不想修改Homeassistant源码，所以方案就是通过Node-RED进行德业MQTT消息的桥接转发，然后再接入Homeassistant。

## 协议
### HTTP API
我们可以通过手机上的“德业智能”App抓包来研究德业服务相关的协议，通过登录过程的抓包我们可以发现登录成功后，会得到一个token，这个token是德业其余需要权限验证的API请求时必要的条件，也就是请求头中需要一个名为“Authorization”的参数，value为"JWT "+token。后继我们就可以通过相关API就可以获取德业MQTT服务的配置信息，以及设备列表。
需要了解整个请求的过程可以参见我写的这个python脚本 [https://github.com/kejinlu/deyeinfo/blob/main/deyeinfo.py](https://github.com/kejinlu/deyeinfo/blob/main/deyeinfo.py)。

### MQTT 设备状态和命令
MQTT的抓包这边建议使用iOS的App来抓，iOS App的MQTT目前是没有走TLS协议的，可以iOS设备连PC分享出来的Wi-Fi，然后PC上使用Wireshark来抓包分析，找到MQTT协议的数据包。
#### 刷新码
首先我们发现进入App之后即使我们不操作，客户端也是不断地发送MQTT消息，每次发送完就可以接收到，服务器端发过来的MQTT消息。其实这个定时不断发送的就是刷新命令，大概是3秒发送一次，德业这边是通过不停的轮询发送MQTT消息的方式来及时刷新设备的状态信息的。


<img width="100%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1663490297464-a1448f2d-89e9-44c0-ba4b-211cfa43bcc3.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_89%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1500%2Climit_0">

所以我们就知道刷新命令了，后面我们也会用到。

<img width="60%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1663490993988-8b49cd10-310c-415f-be8c-c5b0877fd84e.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_36%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1246%2Climit_0">

从刷新命令的详细内容可以看到，MQTT消息内容为0001，而且消息内容的格式并不是字符串而是内存数据，这边还有个注意点就是QoS，这边需要设置为0，德业的服务器端貌似对QoS做了校验，反正我测试的时候如果不是0，则发过去没有作用。
#### 设备状态码
接下来研究下返回的设备状态值，

<img width="60%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1663495783467-5d9f5dd7-edf4-4f79-975e-c8552bd3bb60.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_36%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1274%2Climit_0">

下发设备状态的MQTT消息体解析之后是一个json格式的数据，里面data的内容是一个字符串，这个字符串就是设备的状态码。
14118108101900000000000000000041450000000000
通过设备不同的状态值的比对，我们可以弄清楚哪些数据对应什么内容，这个是需要慢慢设置，然后抓包看数据，进行比照，比较费时间。
需要额外注意的是，不同的设备某些状态值可能并不相同，比如开关机的状态，这些最好自己MQTT抓包确实下。这里的数值都是以DYD-D50B3型号为准。

| 数据片段 | 含义 |
| --- | --- |
| 1411 |  |
| 8 | 风扇水箱状态（0风扇停转，8风扇打开，4水箱满） |
| 1 | 开关机状态（0关机，1开机，B开机并设置了定时关机，6关机并开启了童锁，7开机并开启了童锁） |
| 0 |  |
| 8 | 压缩机状态（0待机，8运行） |
| 1 | 风速（0停转，1低风，2中风，3高风） |
| 0 | 设备模式（0手动，1干衣） |
| 19 | 设定湿度（16进制格式） |
| 000000000000000000 |  |
| 41 | 环境温度（16进制格式，可能是为了表示零下温度，所以转为10进制再减去40才是真实的温度，比如41hex = 65oct   65-40=25,所以25是当前环境温度） |
| 45 | 环境湿度（16进制格式） |
| 0000000000 |  |

#### 设备设置码
通过在App上对除湿机进行各种设置，我们可以抓到对应设置的编码。

<img width="60%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1663494053956-cd5a6615-909f-42fb-a6b4-b97ad3ebe862.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_36%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1248%2Climit_0">

这里可以看到，和刷新码一样，都是二进制格式（图里面是转成了16进制），并不是字符串。
通过各种App的操作设置我们也可以知道他们的各位的含义。

| 数据片段 | 含义 |
| --- | --- |
| 08 02 0 |  |
| 1 | 设备开关 |
| 1 | 风速（1低风，2中风，3高风） |
| 0 | 模式（0手动，1干衣） |
| 4b | 设置湿度（16进制） |
| 00 00 00 |  |

## Node-RED Flows
然后我们将[flows.json](https://github.com/kejinlu/deye-nodered/blob/main/flows.json)导入Node-RED

<img width="60%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1663416912321-f974b598-ae50-45c6-b498-bfefd271a356.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_40%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1402%2Climit_0">

- “德业除湿机状态接受” 设置德业官方MQTT服务的配置以及状态的topic
- “状态转发”节点中设置好本地MQTT服务的配置。
- “自动刷新”节点可以设置主动刷新状态的时间间隔，德业这方便设计的不是特别好，状态的及时更新依赖于本地的轮询刷新命令
- 三个“设置”节点们需要配置好本地mqtt服务
- “德业除湿机命令发送”节点设置好德业官方MQTT命令topic
- “解析状态”和“设置命令”节点中，需要根据自己设备的MQTT协议抓包结果来做相关的修改适配，因为不同设备一些状态码，或者命令码有细微的差别。

## Homeassistant配置及使用
根据[configuration.yaml](https://github.com/kejinlu/deye-nodered/blob/main/configuration.yaml)中的示范配置，将除湿机和相关的传感器配置到自己的Homeassistant的配置中。

<img width="50%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1663416912263-7d27931d-ab17-4566-a62c-7456620d62f3.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_28%2Ctext_5Y2i5YWL%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10">