MQTT由IBM的Andy Stanford-Clark博士和Arcom的Arlen Nipper（现为Eurotech）于1999年发明。
> MQTT代表MQ遥测传输。它是针对受限设备和低带宽，高延迟或不可靠网络设计的发布/订阅，非常简单和轻便的消息传递协议。设计原则是尽量减少网络带宽和设备资源需求，同时还要确保可靠性和一定程度的交付保证。这些原则还可以使协议成为连接设备新兴的“机器对机器”（M2M）或“物联网”世界的理想选择，以及带宽和电池电量非常高的移动应用。
##前言
最近要用arduino开发自主借还书的demo，通过mqtt协议发布数据到服务器，然后用APP订阅相应Topic接收数据，做相应处理。第一步先是搭建mqtt服务器，现在开源mqtt的broker比较多，最后还是选择了Apollo。当然如果不想自己搭建也可以用相应平台，比如阿里云的消息队列中有mqtt，以及物联网平台onenet等等（但是用他们要设置各种key和id，我试了能够连接，但publish始终不行，不知道是哪里设置错了）

##搭建MQTT服务器
1. 下载apollo
下载地址[http://activemq.apache.org/apollo/download.html](http://activemq.apache.org/apollo/download.html)

![](http://upload-images.jianshu.io/upload_images/5734256-9b44ff3349e31a8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 解压得到下面这些文件

![](http://upload-images.jianshu.io/upload_images/5734256-def7eb80037fd3a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

bin下包含apollo和apollo.cmd两个文件

![](http://upload-images.jianshu.io/upload_images/5734256-dd813f6c408be1d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 打开cmd进入到bin目录下，输入
**apollo.cmd create mybroker**
mybroker是服务器名字任意，服务器实例包含了所有的配置，运行时数据等，并且和一个服务器进程关联。（注意：这里需要java环境，没得要下载jdk，并配置环境变量）

![](http://upload-images.jianshu.io/upload_images/5734256-b2041e3931f73894.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

完成后会多出一个mybroker文件夹

![](http://upload-images.jianshu.io/upload_images/5734256-e7a96d437397014d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
其中etc中包含相关配置文件，apollo.xml是配置服务器信息的文件，users.properties文件包含连接MQTT服务器时用到的用户名和密码，后面登录网页会介绍，可以修改原始的admin=password，可以接着换行添加新的用户名密码。

4. 打开mybroker的bin目录，cmd进入到bin目录下，输入
**apollo-broker.cmd run**
开始运行，关闭cmd后会停止。或者可以install并run apollo-broker-service，让它长期在后台运行
![](http://upload-images.jianshu.io/upload_images/5734256-c99d2e1f2d3dbee2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5. 在浏览器中输入[http://127.0.0.1:61680/](http://127.0.0.1:61680/)或[https://127.0.0.1:61681/](https://127.0.0.1:61681/)进入web管理页面，输入默认admin，password

![](http://upload-images.jianshu.io/upload_images/5734256-238a00bc466140ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

队列和发布的Topic

![](http://upload-images.jianshu.io/upload_images/5734256-208a733a18eaf406.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

连上的设备

![](http://upload-images.jianshu.io/upload_images/5734256-56a51da0372bed86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置页面
![](http://upload-images.jianshu.io/upload_images/5734256-f4e4118286f27b51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6. 很多教程说到上面就没了，然后你试着用连接发现根本连不上，其实打开apollo.xml仔细看你会发现少配置了一个选项导致的，可以直接在web页面改

这两处取消注释就可以连接上了，修改后保存会重新载入配置文件

![](http://upload-images.jianshu.io/upload_images/5734256-c76509ece73e5c50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7. 其他
在apollo.xml中

![](http://upload-images.jianshu.io/upload_images/5734256-ea1235a621a6aa33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面两行是web管理登入链接，下面第一个是tcp方式连接是端口，可以自行更改，比如一般默认1883端口
