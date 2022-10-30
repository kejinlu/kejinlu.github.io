---
layout: post
title: 家庭网络分享
categories:
- Homelab
tags:
- network
---

## 选型

### AP还是Mesh？
对于小户型选一个穿墙能力强的路由器放在房子中央位置基本就能全屋覆盖。对于大户型一般就AC+AP  和Mesh两种方案，AC+AP需要提前布好网线连接AP，Mesh的话则相对自由一些，可以通过无线回程进行Mesh组网，但是稳定性上肯定不如AC+AP的方案（有线回程的Mesh估计好点）,而且一般情况下AP的带机量也是远远大于Mesh的。
### 千兆还是万兆？ 
目前来说大部分内网使用场景千兆是完全可以满足需求的比如4K视频播放，VR（比如Quest2无线串流），目前家庭万兆的使用场景一般是万兆NAS在线视频剪辑，需要频繁内网设备之间高速拷贝数字内容，多人同时需要超高速内网访问。
外网宽带目前一般最高也就是千兆，除非你拉专线价格非常高或者多个账号叠加拨号，一般都很少用到。
另外现在好一点的万兆网络设备都价格不菲。
虽然现在不上万兆，但是我们也得为日后的万兆升级做好准备。

## 布线

如果房子是自己装修，那么就可以完全按照自己的想法来，如果是精装修好的房子那么就局限比较多了，顶多就是换换线。
在装修之前我们先要想好弱电箱的位置，然后所有的网线都汇聚在此，如果空间足够，专门整个弱电间也是ok的。机柜或者设备放在柜子里面的，需要提前考虑好散热风道之类的，可以打散热孔加散热风扇。

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952711348-53d077c2-4c3f-4c6a-8b44-02a3195bd1d1.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u7524e969&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u5bf9b74d-4d0f-4e9a-bc56-e82fddbefb3&title=">

我这边是设计了储藏间当弱电间，弱电间放机柜，然后由于从地下室机柜端拉网线到二层三层网线会非常长，所以二层设置一个弱电箱 作为一个交换机中继节点，二楼三楼的网络端口都汇聚到这个弱电箱。

<img width="20%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952711903-614fc23e-2fa3-4f65-8a53-96e68e125b5e.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=uc9db4478&margin=%5Bobject%20Object%5D&originHeight=855&originWidth=627&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u107cbd48-a28e-42b9-b2f3-4246db1ba93&title=">


网线六类足够了，50米的六类线万兆的速度是绝对没有问题的，另外尽量不要用屏蔽线，除非你能确保屏蔽线都能正确的接地。主干节点之间如有必要布网线的同时也可以布上光纤，万兆网络时，光口光纤无论能耗发热都优于电口网线。 其他要注意的点就是 汇聚到机柜端的网线的收口的美观，你可以使用空白面板，
如果网线太多，86面板盒塞不下也可以不用线盒，直接墙壁上安装防水盒，防水盒开孔来引出网线。

剩下就是所有网线提前做好规划，进行布线

- 规划好网口面板的位置，比如电视机，电脑，别的有线网络设备等，每个房间都需要预留网口
- 提前规划好AP的位置，吸顶，壁挂或者面板AP，所有的AP都通过POE进行供电
- 提前规划好户外POE摄像头位置
- 特定设备布上网线到弱电箱或者机柜（因为网线八根铜线有时候可以用作信号线，比如用于传输智能电rs485信号线 或者中央空调到空调控制器的信号线）


## 网络设备

最终选择的是AP模式，AP企业产品比较多，近些年也有不少家用的AP产品，常见的AP品牌也有不少，TPLink，华三，锐捷，aruba，unifi(UBNT),综合工业设计，系统软件等各方面因素，选择了Unifi全家桶。U家的产品特点主要是苹果风格的工业设计，复杂功能抽象封装，一些高级的功能只需要简单的配置即可。
Unifi的AP应该都是胖瘦一体的AP，支持软AC，也支持脱离AC独立运行。目前软AC支持的部署方式也很多，有pc上的软件客户端，或者docker进行部署，ac软件设置好之后，即使脱离了AC，AP也能正常工作，这两年官方都在推自带AC软件的UnifiOS设备，比如UDM，UDM Pro，UDM SE等设备，这些设备集成了路由器，交换机，UnifiOS（包含了AC管理）。

目前的拓扑结构如下：

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952712196-5e4b4143-0142-45bb-8028-3c6f6ef72704.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=418&id=udc24456c&margin=%5Bobject%20Object%5D&originHeight=835&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ub224e0bb-bf39-4b32-909a-07c423378a1&title=&width=500">


### 地下室机柜
路由器和主交换机

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952712243-aa1733bc-3e68-4585-9c55-c9e7a1e14996.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u3c3670bb&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=uf96a624d-8383-46fd-80b8-85d401adea1&title=&width=500">


其他设备

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952712511-6aeeaf03-e163-4f5d-af94-22856ad4ff10.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u9679ad31&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ubfcabf50-a3a2-49be-bf98-d2e579ba6f6&title=&width=500">

机柜主要核心设备就是路由器和交换机
**路由器** 之前一直用的USG3P加上docker部署的unifi controller（unifi network）来对unifi网络进行控制，为了事情变得简单以及更好的和机柜搭配，所以后来升级了UDM SE，自带UnifiOS（包含了软AC，Unifi Network套件），这样就可以摆脱dcoker部署的Unifi Network。
**主交换机** 一般情况下我们只需要2层交换机就够了，如果存在需要POE驱动的设备比如POE摄像头，AP，那么就需要带POE的交换机了，POE协议发展到今天有大概分三类，802.3af、802.3at、802.3bt，一般交换机用到的进阶特性无非就是链路聚合和VLAN，
机柜中还有的设备包含**散热风扇**，**UPS+NAS**，**软路由**，**监控录像机**以及其他的一些智能家具的网关设备。
因为NAS中的机械硬盘对突然掉电比较敏感，容器损坏硬盘影响硬盘寿命，所以一般情况下NAS最好都配一个UPS，这边用的是APC的BK650M2-CH，可以和NAS搭配工作，当断电后，NAS会接收到UPS的通知，然后进行关机保护，NAS和主交换机通过两根网线进行链路聚合，提高带宽。另外NAS下面可以放减震垫，毕竟4个硬盘同时工作多多少少有些震动的😂。
软路由则安装了PVE，然后PVE中安装各种需要的虚拟机，比如用于部署各种Docker的RancherOS系统，作为旁路由角色的OpenWRT，用于管理智能家居的Home Assistant OS。

<img width="20%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952712303-7dec49dc-ca7c-44a1-a6b1-754913302822.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=276&id=u084ec47c&margin=%5Bobject%20Object%5D&originHeight=552&originWidth=402&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ue89826ef-a9fe-452d-a0cd-b5ea8c07e95&title=&width=201">

### **二楼中继弱电箱**

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952712668-40dcead4-693e-418f-b1ea-ca6042c9fa81.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=uc6366a41&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u5614dc84-484f-46ec-b7fd-575b235141f&title=&width=500">

二楼弱电箱主要设备就一个POE交换机和散热风扇，主要负责链接主交换机以及二楼和三楼的所有网络设备，由于目前没有使用万兆设备，这边和主交换机使用两根网线进行链路聚合，所以带宽可以达到2GbE，也就是如果两个AP同时访问NAS的话，整体带宽会突破1GbE。
### **AP**
AP算是日常使用用比较核心的设备了，毕竟现在很多设备都是WiFi连接的。Unifi Network可以很方便的对所有AP进行集中管理，一般情况下我们可以根据使用场景来创建多个WiFi Network，不同WiFi网络可以绑定不同的Network（不同的Network可以对应不同的VLAN），比如我们可以单独设置一个WiFi 出来进行科学上网，连上这个无线网就可以科学上网了，而且这个网络所有的设置更改都不影响默认的正常的WiFi网络。
以下是各个位置的AP布置：
**地下室** 0F(UAP-AC-IW) 面板型AP

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952712659-39751971-cc7f-4b6b-9d5d-b0ac23533914.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u41a46f80&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u87f2e711-5ec9-44bb-81d0-f30e1b7c651&title=&width=500">

**餐厅** 1F-N(UAP-AC-LR) 吸顶AP 放置在餐厅位置，兼顾厨房餐厅厕所以及厕所外的设备平台等空间，LR型号的穿墙能力稍强

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952712836-584de71d-cd0a-4a48-88d1-d5c23b79b677.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u66340c13&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ud7462bb8-d848-409a-ac1d-8df1cb3cff6&title=&width=500">

**客厅** 1F-S(UAP-AC-Pro) 吸顶AP 放置在客厅靠窗户的位置，兼顾客厅和院子， Pro型号的带机量以及带宽会比较高

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952712872-b075bcda-8e7b-4d6d-b294-c196049b696d.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u9301dd9d&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u85719376-cb1f-4414-9e7a-d3454b46848&title=&width=500">

1F(USW-Flex-Mini) 客厅这边同时布置了一个小的交换机，毕竟无线再快也没有有线稳定，所以Apple TV，PS5啥的还是连的有线，还有IPTV的机顶盒需要使用有线和VLAN的特性，交换机也是必要的。

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952713184-acc857ed-745a-4617-8919-5e274decc36c.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u150d369f&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u7fe8f5f3-3f3a-47a9-8f65-413ce3e43b4&title=&width=500">

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952713291-3a4437a3-a729-4dcb-b517-39dd84bef74d.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u4480de83&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u11654458-4a81-40f2-b15a-01e5743f235&title=&width=500">


**二楼北**  2F-N(UAP-FlexHD) 主要供北面的房间和书房使用，FlexHD的信号强度以及无线速度都比较强

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952713288-54d45e34-bd23-4b0c-9333-db66c51c8de4.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=ud519ba0b&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u3d0a7045-80ec-4614-9dc7-0e634fcea1d&title=&width=500">

**二楼南** 2F-S(UAP-AC-M) 南面的房间使用

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952713417-a05bbdf9-cf69-499a-b76f-14f88ef5f47b.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u0ad27754&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u08f6eaa5-0145-40e2-a329-323eac0aed6&title=&width=500">

**三楼** 3F(UAP-AC-Pro) 顶楼房间使用

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952713494-2019cec8-9830-4ec3-b498-d86c23369359.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u474212d8&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u604c15f1-e108-4e7f-b456-2832ee24018&title=&width=500">

**入户** Frontyard(UAP-AC-Lite)  这边是通过USW Flex交换机（放在摄像头上方的防水盒内），来同时对摄像头和AP供电，而USW Flex自身也是通过POE（af，at或者bt）进行供电的，这种模式用在户外一分多的情况下很方便，避免了电源线的布置，只需要一根网线即可搞定多个设备的网络和供电。

<img width="30%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952713850-546f02ab-195d-4c56-a085-04a613dce58b.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=300&id=ub3cfea0f&margin=%5Bobject%20Object%5D&originHeight=600&originWidth=600&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ud05536be-957f-4b39-83ad-fb13f9672ff&title=&width=300">

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952713845-95b116a2-a929-430f-9110-95df296b2160.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=334&id=u3675e3c9&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ud633df13-1244-46d3-9745-be59ad21d29&title=&width=500">


## 使用场景举例

### 影视多媒体
还记得以前看下载的电影或者电视剧都是直接放在NAS上用本地的普通播放器看。后来知道了Jellyfin或者Plex这类媒体服务器的概念，媒体服务器可以对电影电视剧进行统一的管理，自动刮削电影或者电视剧的元数据。在多个客户端上观看记录和状态都可以自动进行同步，你在电视上看的记录，到手机或者电脑上观看进度都在，随时继续观看。
我这边使用的是开源免费的Jellyfin，客户端一般用的Infuse和官方的客户端。
Jellyfin服务直接部署在QNAP NAS的Docker的容器中，这样可以直接挂载NAS的硬盘。

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952713903-983b3fb6-cbce-4b68-ad4d-4806a4970877.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=ud160401f&margin=%5Bobject%20Object%5D&originHeight=588&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u6c5da597-dcdf-4ba2-a5cc-841a2cf7968&title=">


Infuse Pro客户端（Apple TV）

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/jpeg/328998/1661952714064-7ec82458-10d5-4625-8a83-7c56bcc1056c.jpeg#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=ufc376156&margin=%5Bobject%20Object%5D&originHeight=667&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u25442c1c-e22f-49d4-b7ba-7f2655e64c6&title=">

Jellyfin网页端

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952714090-7492ab51-f414-4d01-a21b-cd39f5e4bf56.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=ue80245cc&margin=%5Bobject%20Object%5D&originHeight=1147&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u8a489c29-e4c6-4e4a-a8f7-ecc1f944f6b&title=">

每一集的简介信息

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952714203-b6c2916b-9371-41c4-a8fa-64f416bbaa26.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u2b4c28f2&margin=%5Bobject%20Object%5D&originHeight=1169&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u1713fc75-217a-4c3b-b824-2420a5a2316&title=">

另外不要使用服务器端解码，端上解码是最好的解决方案。
### 远程访问
DDNS
DDNS 是使用的阿里云的服务，通过OpenWRT的服务来进行更新，当wan口ip地址发生变化的时候就会自动更新域名ip地址。当然这个需要你的互联网提供商能够给你分配外网IP地址。

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952714428-c2a16bfa-a3cb-487d-990a-9c4085534731.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u9cb08415&margin=%5Bobject%20Object%5D&originHeight=1438&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ue68157b0-f570-4518-ac47-638a71037ad&title=">

VPN
有ddns的帮助，我们就可以通过域名访问家里的ip地址了，如果需要访问家中的一些服务，我们可以通过端口转发的方式进行，端口转发这功能一般的路由器都提供了，但是端口转发存在一定的安全性，直接将相关服务暴露在互联网上，所以为了安全起见，还是走VPN比较安全。
一般的路由器也是提供了VPN服务的，Unifi VPN的设置

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952714546-57bb9915-15d7-4b64-9ad8-1cee305e5193.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u3071be38&margin=%5Bobject%20Object%5D&originHeight=806&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u34aabac2-bbbd-44f9-a33a-28fd27baed3&title=">

这样电脑或者手机上就可以通过域名和L2TP相关的信息设置 VPN连入家里的网络了。
### 科学上网
为了保证默认WiFi网络的稳定，默认网络不提供科学上网，所以单独划出一个WiFi来给科学上网使用，这样这个单独的WiFi再怎么折腾也不会影响正常的网络。
软路由的OpenWRT上安装OpenClash服务（别的类似服务也ok），然后OpenWRT以旁路由的角色接入网络（LAN口接入交换机），如果多个VLAN网络都需要使用相关服务，那么可以添加多个网络接口，保证OpenWRT在相应的VLAN网络中能够访问。

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952714707-933315d2-dedb-45b1-a07b-47bb340453a3.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=uc525d0f2&margin=%5Bobject%20Object%5D&originHeight=863&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ubd33b3b2-5348-4e2c-b8f0-ebeba902bef&title=">

然后我们设置单独划出来的WiFi网络
我们先创建一个Network，对其进行相关配置，设置相应的网段和VLAN ID

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952714636-89b84b2d-175a-4b90-a459-b4a51ecb3abb.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=ubc8ff27c&margin=%5Bobject%20Object%5D&originHeight=666&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u4d5045a2-d923-4854-9b90-9e515d669b6&title=">

设置DHCP的DNS Server和Geteway为旁路由的地址，这样客户端脸上这个网络之后 就会将网关和DNS Server设置为相应的IP地址。

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952714966-34d7726e-b785-4edc-b2fb-e3d67b31724e.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u42f65f19&margin=%5Bobject%20Object%5D&originHeight=684&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u8bec8252-e384-482a-8484-d46e0d59a0d&title=">

网络设置好了之后，我们再去设置WiFi，选择刚才创建好的Network，这样创建出来的WiFi就可以进行科学上网了。

<img width="80%" loading="lazy" referrerpolicy="no-referrer" rel="noreferrer" src="https://cdn.nlark.com/yuque/0/2022/png/328998/1661952715097-84c838ef-0b11-43cb-badd-55c6e174134d.png#clientId=u9290e070-0038-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=u14898319&margin=%5Bobject%20Object%5D&originHeight=898&originWidth=1000&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u88ddc50a-da84-40d5-8c3f-148d701ecc8&title=">

### iTV
目前iTV也就用来看看比赛，平时也比较少看。 通过VLAN，即使客厅电视和机柜只有一根网线我们可以方便的通过交换机机顶盒来连光猫的IPTV口拨号看电视。不少地方的iTV 你可以进行抓包，然后通过OpenWRT模拟拨号，然后再通过udpxy服务将多播转为内网单播，随处通过rtsp流进行观看，网上资料很多，这里就不多做陈述了。
### IoT
IoT网络这块其实没啥好说的，就是如果想要提高安全性，可以为IoT设备单独划一个VLAN网络，和一般上网用的网络进行隔离。我目前为了简单还没有这么做，我一般的诸如智能开关，插座，zigbee网关啥的基本都是买的sonoff家的产品然后刷的tasmota的开源固件，这样就可以得到设备的完全控制权，然后再集成中Home Assistant系统中，其他很多小米的设备也可以集成到HA中，如果有同学想详细了解相关内容，我可以单独再开一篇来写。
