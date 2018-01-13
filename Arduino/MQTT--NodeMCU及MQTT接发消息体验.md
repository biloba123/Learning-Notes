使用MQTT先要有一个MQTT服务器，具体如何搭建可以看上一篇文章http://www.jianshu.com/p/e11a47f1e53c

这一篇主要讲下NodeMCU的使用，以及MQTT介绍和如何接发消息

##NodeMCU
###1. ESP8266介绍
介绍NodeMCU前需要先了解ESP8266，它是一个完整自称体系的WiFi网络解决方案，能独立运行也可为从部件连接单片机运行

具有以下特点：
- 超小尺寸
- 低功耗
- 内置TCP/IP协议
- 可编程
- 低成本

###2. NodeMCU介绍
NodeMCU是一款基于ESP8266模块的开源硬件，符合Arduino框架。同时可使用Node.js编程

![NodeMCU](http://upload-images.jianshu.io/upload_images/5734256-f76e3ee2bd0a2c78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![引脚](http://upload-images.jianshu.io/upload_images/5734256-b146c6aeacefa406.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###3. WiFi测试
先要安装Arduino IDE For ESP8266
Arduino IDE For ESP8266是根据Arduino修改的专门烧写ESP8266开发板的IDE。在装好Arduino IDE后：
1. 打开Arduino 文件->首选项，在 附加开发管理网站 中填入http://arduino.esp8266.com/stable/package_esp8266com_index.json，然后点击确定保存

![](http://upload-images.jianshu.io/upload_images/5734256-e9d6b0b4bc008b9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 重启IDE后，打开 工具->开发板->开发板管理器；搜索ESP8266，选择esp 8266 by ESP8266 Community安装

![](http://upload-images.jianshu.io/upload_images/5734256-aebe695f0a4fbea8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 下载完成后可以在开发板选项中看到ESP8266 Module,以及NodeMCU等可选开发板

- 将NodeMCU通过usb连接到电脑，在工具下选择相应配置

![](http://upload-images.jianshu.io/upload_images/5734256-e5d0a735273d4681.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

波特率越大烧录程序速度越快但有可能出错
端口选择NodeMCU对应端口，如果没看到端口，那是驱动没有装，装驱动可以看这里http://www.arduino.cn/thread-1008-1-1.html

4. 打开示例选择ESP8266WiFi中的WiFiScan

![](http://upload-images.jianshu.io/upload_images/5734256-976b7c3babf87b5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

烧到板子上打开窗口监视器可以看到扫描出来的附近热点

![](http://upload-images.jianshu.io/upload_images/5734256-6523a0a7a15c39dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##MQTT
###1. MQTT介绍
消息队列遥测传输(MQTT)是IBM开发的即时通讯协议，为计算能力有限且工作在低带宽、不可靠网络的传感器或控制设备而设计。比如对于移动开发，它可以用于消息推送，即时通讯等等

**特性：**
- 发布/订阅的消息模式，提供一对多的消息发布
- 使用TCP/IP提供网络连接
- 有三种消息发布服务质量，至多一次，至少一次，只有一次
- 传输小、开销小
- LastWill通知中断机制

###2. MQTT原理介绍
- 客户端：发布者(Publish)、订阅者(SubScribe)，客户端有ID,ID冲突会挤掉先连接客户端。
- 服务器端：代理(Broker)
- 消息：主题(Topic)+负载(payload)

![](http://upload-images.jianshu.io/upload_images/5734256-aa7c5afc06cb32e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

举个场景为例：

![](http://upload-images.jianshu.io/upload_images/5734256-923125333073a289.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

QQ用户2(账号QQ1000)向QQ用户1(QQ9999)发送消息“Hello World”.
发送者：QQ用户2
订阅者：QQ用户1
消息：QQ9999+”Hello World”.

消息发送至服务器，服务器查找QQ9999对应的用户后，发送信息给QQ用户2.

###3. MQTT ESP8266库
菜单“项目”-“加载库”-“管理库”，搜索安装“PubSubClient”

![](http://upload-images.jianshu.io/upload_images/5734256-3a3ea6f9bdea82e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

PubSubClient有一些示例可以打开mqtt_esp8266看下

###4. MQTT接发消息体验
这里做两个示例
**1. NodeMCU发布消息：光照强度传感器测得环境光照强度发布到服务器，手机订阅该Topic接收光强信息**

```
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
int pinLED = D2;

const char* ssid = "********";//连接的路由器的名字
const char* password = "********";//连接的路由器的密码
const char* mqtt_server = "********";//服务器的地址 
const int port=****;//服务器端口号

WiFiClient espClient;
PubSubClient client(espClient);

int light=255;

void setup_wifi() {//自动连WIFI接入网络
  delay(10);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print("...");
  }
}

void callback(char* topic, byte* payload, unsigned int length) {//用于接收数据
  int l=0;
  int p=1;
  for (int i = length-1; i >=0; i--) {
    l+=(int)((char)payload[i]-'0')*p;
    p*=10;
  }
  light=l;
  Serial.println(l);//换行

}

void reconnect() {//等待，直到连接上服务器
  while (!client.connected()) {//如果没有连接上
    if (client.connect("biloba")+random(999999999)) {//接入时的用户名，尽量取一个很不常用的用户名
      client.subscribe("LED");//接收外来的数据时的intopic
    } else {
      Serial.print("failed, rc=");//连接失败
      Serial.print(client.state());//重新连接
      Serial.println(" try again in 5 seconds");//延时5秒后重新连接
      delay(5000);
    }
  }
}


void setup() {//初始化程序，只运行一遍
  Serial.begin(9600);//设置串口波特率（与烧写用波特率不是一个概念）
  setup_wifi();//自动连WIFI接入网络
  client.setServer(mqtt_server, port);//端口号
  client.setCallback(callback); //用于接收服务器接收的数据
}



void loop() {//主循环
   reconnect();//确保连上服务器，否则一直等待。
   client.loop();//MUC接收数据的主循环函数。
   analogWrite(pinLED,light);
}

```
先使用ESP8266WiFi库的方法连接一个wifi，连接成功后loop中获取光强并转换为json格式，发布到light的Topic主题下，可以打开服务器web管理页面，可以在Topic找到它


![](http://upload-images.jianshu.io/upload_images/5734256-ca912322e21209c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接着试着用手机app订阅接收传感器发的消息（具体Android端如何实现下篇文章会讲到），在google play中下载一个myMqtt的APP，不方便的可以在这里下http://pan.baidu.com/s/1slE0cw9

![](http://upload-images.jianshu.io/upload_images/5734256-a1977b4df2a183a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)

打开APP，在setting中设置你服务器ip，端口等保存，如果顶部显示绿色表明连接成功，在Subscribe中订阅light主题，返回到Dashboard就能实时收到消息


![](http://upload-images.jianshu.io/upload_images/5734256-7c3b1d6129b14cc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)

**1. NodeMCU接收消息：连接一个led灯，用手机APP控制灯的亮度**
```
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <math.h>
int pinLED = D2;

const char* ssid = "********";//连接的路由器的名字
const char* password = "********";//连接的路由器的密码
const char* mqtt_server = "********";//服务器的地址 
const int port=****;//服务器端口号

WiFiClient espClient;
PubSubClient client(espClient);

int light=1024;//led的亮度值

void setup_wifi() {//自动连WIFI接入网络
  delay(10);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print("...");
  }
}

void callback(char* topic, byte* payload, unsigned int length) {//用于接收数据
  //将char* 转换为int
  int l=0;
  int p=1;
  for (int i = length-1; i >=0; i--) {
    l+=(int)((char)payload[i]-'0')*p;
    p*=10;
  }
  light=l;//赋值给light
  Serial.println(l);

}

void reconnect() {//等待，直到连接上服务器
  while (!client.connected()) {//如果没有连接上
    if (client.connect("biloba")+random(999999999)) {//接入时的用户名，尽量取一个很不常用的用户名
      client.subscribe("LED");//接收外来的数据时的intopic
    } else {
      Serial.print("failed, rc=");//连接失败
      Serial.print(client.state());//重新连接
      Serial.println(" try again in 5 seconds");//延时5秒后重新连接
      delay(5000);
    }
  }
}


void setup() {//初始化程序，只运行一遍
  Serial.begin(9600);//设置串口波特率（与烧写用波特率不是一个概念）
  setup_wifi();//自动连WIFI接入网络
  client.setServer(mqtt_server, port);//端口号
  client.setCallback(callback); //用于接收服务器接收的数据
}



void loop() {//主循环
   reconnect();//确保连上服务器，否则一直等待。
   client.loop();//MUC接收数据的主循环函数。
   analogWrite(pinLED,light);//设置led亮度
}

```
代码跟上面没太大区别，只不过这里不发信息了，而接收LED Topic的信息，接收到转换为int设置为灯的亮度，这样就实现远程app控灯。打开myMqtt的publish，输入Topic为LED，Message为灯的亮度（0-1024），可以实时控制灯

![](http://upload-images.jianshu.io/upload_images/5734256-b3b86a653aac87f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)


![1024](http://upload-images.jianshu.io/upload_images/5734256-42acf9547f5dbcba.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)


![100](http://upload-images.jianshu.io/upload_images/5734256-45583bdf40140037.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)


![0](http://upload-images.jianshu.io/upload_images/5734256-e2519a594b7b13df.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)
