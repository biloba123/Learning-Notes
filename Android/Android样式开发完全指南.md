记得刚开始学Android时，看着自己完全用系统控件写出的不忍直视的界面，对于如何做出不一样的按钮，让它们在不同状态下有不一样的效果很是好奇。后来才知道了些shape，selector之类，但很长时间以来都没对样式开发做过全面的整理（主要因为懒...），所以有了这篇文章。

好了废话不多说，开始我们的样式开发之旅。因为整理了基本所有的样式，文章可能有点长，可以收藏下来看，当然看的时候也要自己试试效果，不然很容易过几天又忘记了，下次用的时候还要翻一遍。
本文将按下面几个模块讲：
**1. shape：基础的形状定义**
**2. selector：不同状态下的形状变化**
**3. layer-list：多个形状层叠**
**4. drawable：各式drawable和drawable动画**
**5. View Animation：简单的视图动画**
**6. Property Animation：更强大的属性动画**
**7. style和theme**

##1. shape
shape用来定义基本的形状，一般项目的drawable目录下，布局中各view通过设置android:background属性来引用。

###1.1 rectangle（矩形，默认的形状）
新建一个drawable resource file，取名为bg_btn，来为按钮定义形状，根节点默认是selector，改成shape，shape下能设置多个特性，我们一个一个来看

**solid**：设置形状填充的颜色，只有android:color一个属性
```
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="@android:color/holo_blue_dark"/>
</shape>
```
效果和直接设置view的background为颜色一样

![](http://upload-images.jianshu.io/upload_images/5734256-8334c20f0b9a4986.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**padding**：设置内容与形状边界的内间距，可分别设置左右上下的距离
- android:left 左内间距
- android:right 右内间距
- android:top 上内间距
- android:bottom 下内间距
```
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="@android:color/holo_blue_dark"/>
    <padding android:top="16dp"
             android:bottom="16dp"/>
</shape>
```
当然也和直接设置view的padding一样，不过当你的shape多次使用时，在shape中设置padding就免去为每个控件单独设置padding
![](http://upload-images.jianshu.io/upload_images/5734256-bef56bf46605fd2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**gradient**：设置形状的渐变颜色，可以是
*线性渐变（linear，默认的渐变类型）*
*辐射渐变（radial ，以圆形从圆形向周围渐变，android:gradientRadius也必须设置）*
*扫描性渐变（sweep ,以一条水平线逆时针扫描渐变）*
- android:type 渐变的类型
- android:startColor 渐变开始的颜色
- android:endColor 渐变结束的颜色
- android:centerColor 渐变中间的颜色
- android:angle 渐变的角度，线性渐变时才有效，必须是45的倍数，0表示从左到右，90表示从下到上
- android:centerX 渐变中心的相对X坐标，放射渐变时才有效，在0.0到1.0之间，默认为0.5，表示在正中间
- android:centerY 渐变中心的相对X坐标，放射渐变时才有效，在0.0到1.0之间，默认为0.5，表示在正中间
- android:gradientRadius 渐变的半径，渐变类型为radial时使用

**linear渐变效果**
```
<gradient android:type="linear"
              android:startColor="#18d1ff"
              android:centerColor="#4953fd"
              android:endColor="#5718bc"
        android:angle="0"/>
</shape>
```
![](http://upload-images.jianshu.io/upload_images/5734256-26be370dd0834dd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里的渐变方向由android:angle来设置

![45度时](http://upload-images.jianshu.io/upload_images/5734256-73acd06ed9f7c43d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

![-90度时](http://upload-images.jianshu.io/upload_images/5734256-1757c18df0ea487f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

**radial渐变效果**
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient android:type="radial"
              android:startColor="#18d1ff"
              android:centerColor="#4953fd"
              android:endColor="#5718bc"
        android:gradientRadius="160dp"/>
</shape>
```

![](http://upload-images.jianshu.io/upload_images/5734256-02c54ca4e239396f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

渐变中心由(centerX, centerY)确定，比如我们都把它设为0

![](http://upload-images.jianshu.io/upload_images/5734256-8ac571589b518c50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

**sweep渐变效果**
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient android:type="sweep"
              android:startColor="#18d1ff"
              android:centerColor="#4953fd"
              android:endColor="#5718bc"/>
</shape>
```

![](http://upload-images.jianshu.io/upload_images/5734256-b715f0d1a82b9f62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

**corners**：设置圆角，只适用于rectangle类型
- android:radius 圆角半径，会被下面每个具体的圆角属性覆盖
- android:topLeftRadius 左上角的半径
- android:topRightRadius 右上角的半径
- android:bottomLeftRadius 左下角的半径
- android:bottomRightRadius 右下角的半径
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient android:startColor="#0564fe"
              android:centerColor="#3b45ef"
              android:endColor="#6f28e0"/>
    <corners android:radius="24dp"/>
</shape>
```

![](http://upload-images.jianshu.io/upload_images/5734256-b2c3419c00721cb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分别设置topLeftRadius，topRightRadius，bottomLeftRadius

![](http://upload-images.jianshu.io/upload_images/5734256-e4fd99e7c4a939ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**stroke**：设置描边，实线或虚线
- android:color 描边的颜色
- android:width 描边的宽度
- android:dashWidth 设置虚线时的横线长度
- android:dashGap 设置虚线时的横线之间的距离
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <stroke android:color="@android:color/darker_gray"
            android:width="2dp"/>
</shape>
```

![实线](http://upload-images.jianshu.io/upload_images/5734256-db89615b5b89d827.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <stroke android:color="@android:color/darker_gray"
            android:width="2dp"
            android:dashWidth="4dp"
        android:dashGap="6dp"/>
</shape>
```

![虚线](http://upload-images.jianshu.io/upload_images/5734256-128034eeb953453f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###1.2 oval（椭圆形，常用来画圆形）
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <solid android:color="@android:color/holo_blue_dark"/>
    <gradient android:type="radial"
            android:startColor="#ffffff"
              android:endColor="#a1a1a1"
            android:gradientRadius="64dp"/>
    <size android:height="128dp"
          android:width="128dp"/>
</shape>
```
这里有个size节点，它是用来设置形状大小的，但一般不用它，比如我们现在把这个圆形设置给一个长宽不相等的控件，那效果还是椭圆，因为shape会被拉伸或压缩。不过我用到radial渐变，想让它从圆心刚好渐变到圆周，所以设置了size，并设置gradientRadius为size长宽的一半，这样无论设置给多大的控件都能正确显示

![](http://upload-images.jianshu.io/upload_images/5734256-197cf045ba8cce29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###1.3 line（线形，画实线和虚线）
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="line">
    <stroke android:color="@android:color/darker_gray"
            android:width="2dp"
            android:dashWidth="4dp"
            android:dashGap="6dp"/>
</shape>
```
画线一般用来分隔内容，上面我们画了一条虚线，把它设置给View，运行出来你发现怎么变成实线了，我要的虚线呢，这不是坑吗？？？ （╯‵□′）╯︵┴─┴
原来要想显示出虚线要给View设置android:layerType，为"software"
```
<View
        android:layout_width="match_parent"
        android:layout_height="16dp"
        android:layerType="software"
        android:background="@drawable/bg_div"/>
```

![](http://upload-images.jianshu.io/upload_images/5734256-fa6518b7d0ae07aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**画线时，有几点特性必须要知道的：**
1. 只能画水平线，画不了竖线；
2. 线的高度是通过stroke的android:width属性设置的；
3. size的android:height属性定义的是整个形状区域的高度；
4. size的height必须大于stroke的width，否则，线无法显示；
5. 线在整个形状区域中是居中显示的；
6. 线左右两边会留有空白间距，线越粗，空白越大；
7. 引用虚线的view需要添加属性android:layerType，值设为"software"，否则显示不了虚线。

###1.4 ring（环形，可以画环形进度条）
shape根元素有些属性只适用于ring类型，先来看看这些属性吧：
- android:innerRadius 内环的半径
- android:innerRadiusRatio 浮点型，以环的宽度比率来表示内环的半径，默认为3，表示内环半径为环的宽度除以3，该值会被android:innerRadius覆盖
- android:thickness 环的厚度
- android:thicknessRatio 浮点型，以环的宽度比率来表示环的厚度，默认为9，表示环的厚度为环的宽度除以9，该值会被android:thickness覆盖
- android:useLevel 一般为false，否则可能环形无法显示，只有作为LevelListDrawable使用时才设为true
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="ring"
       android:innerRadiusRatio="3"
       android:thicknessRatio="12"
       android:useLevel="false">
    <gradient
        android:startColor="@android:color/white"
        android:endColor="@android:color/holo_blue_dark"
        android:type="sweep" />
    <stroke
        android:width="2dp"
        android:color="@android:color/darker_gray" />
</shape>
```
*用innerRadiusRatio和thicknessRatio控制环内环和厚度占比是，两个倒数相加不能超过1/2，不然就不是环了，比如这里1/3 + 1/12 < 1/2*

![](http://upload-images.jianshu.io/upload_images/5734256-c97c7b6847d084e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但这进度条不会转叫什么进度条，下面会讲到如何让它转起来

##2. selector
上面我们讲了如何定义各种形状，并设置给控件，但开发中如果想让在不同状态下展示不同形状，比如按钮正常状态下一种形状，点击是另一种形状，这样对用户交互比较好，能让用户得到他操作后的反馈，这就要用到selector。

那首先有必要知道都有哪些状态：
- **android:state_enabled**: 设置触摸或点击事件是否可用状态，一般只在false时设置该属性，表示不可用状态
- **android:state_pressed**: 设置是否按压状态，一般在true时设置该属性，表示已按压状态
-  **android:state_selected**: 设置是否选中状态，true表示已选中，false表示未选中
- **android:state_checked**: 设置是否勾选状态，主要用于CheckBox和RadioButton，true表示已被勾选，false表示未被勾选
- **android:state_checkable**: 设置勾选是否可用状态，类似state_enabled，只是--state_enabled会影响触摸或点击事件，而state_checkable影响勾选事件
- **android:state_focused**: 设置是否获得焦点状态，true表示获得焦点，默认为false，表示未获得焦点
- **android:state_window_focused**: 设置当前窗口是否获得焦点状态，true表示获得焦点，false表示未获得焦点，例如拉下通知栏或弹出对话框时，当前界面就会失去焦点
- **android:state_activated**: 设置是否被激活状态，true表示被激活，false表示未激活，API Level 11及以上才支持，可通过代码调用控件的setActivated(boolean)方法设置是否激活该控件
- **android:state_hovered**: 设置是否鼠标在上面滑动的状态，true表示鼠标在上面滑动，默认为false，API Level 14及以上才支持

一看到这么多状态是不是感觉头疼，不过平时用到比较多的也就 state_enabled，state_pressed，state_selected，输入框可能会用到state_focused

接下来，看看代码，以下是bg_btn_selector.xml的代码，用于按钮的背景，如果只是背景颜色的变化可以在res下新建一个color文件夹在里面创建selector作为color资源，如果是形状的变化则只能放到drawable下作为drawable资源，也是通过android:background设置给view：

**color\bg_btn_selector.xml**
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_enabled="false" android:color="@android:color/darker_gray"/>
    <item android:state_pressed="true" android:color="@android:color/holo_blue_dark"/>
    <item android:state_selected="true" android:color="@android:color/holo_blue_dark"/>
    <item android:color="@android:color/holo_blue_light"/>
</selector>
```
android:color只能引用@color

**drawable\bg_btn_selector.xml**
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_enabled="false" android:drawable="@drawable/bg_btn_disable"/>
    <item android:state_pressed="true" android:drawable="@drawable/bg_btn_pressed"/>
    <item android:state_selected="true" android:drawable="@drawable/bg_btn_selected"/>
    <item android:drawable="@drawable/bg_btn_normal"/>
</selector>
```
android:drawable指定的都是定义的shape，它除了引用@drawable资源，也可以引用@color颜色值

![正常状态](http://upload-images.jianshu.io/upload_images/5734256-68d9fe89670abc6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![按压状态](http://upload-images.jianshu.io/upload_images/5734256-9aa71d08720180a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![不可用时](http://upload-images.jianshu.io/upload_images/5734256-e69151745bb3df2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意**：状态是从上往下匹配的，如果匹配到一个item那它就将采用这个item，而不是采用最佳匹配的规则；所以设置默认的状态，一定要写在最后，如果写在前面，则后面所有的item都不会起作用了。

除此之外，selector标签下有两个比较常用的属性，会在状态改变时出现淡入淡出效果，但必须在API Level 11及以上才支持：
- android:enterFadeDuration 状态改变时，新状态展示时的淡入时间，以毫秒为单位
- android:exitFadeDuration 状态改变时，旧状态消失时的淡出时间，以毫秒为单位

##3. layer-list
shape只能画出矩形，椭圆，线，环这些基本的形状，有时我们需要一些稍微复杂些的形状，一种解决方案是使用图片，但图片要准备不同尺寸以适应不同分辨率手机，太多图片资源会造成安装包过大，其实可以用layer-list来自己实现一些复杂形状

先来看看要实现效果

![](http://upload-images.jianshu.io/upload_images/5734256-bfd230a842b3e88f.gif?imageMogr2/auto-orient/strip)

一个带阴影的button，未点击时阴影窄且深，按压时阴影变宽且颜色变浅。它首先是selector实现
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" android:drawable="@drawable/bg_btn_pressed"/>
    <item android:state_selected="true" android:drawable="@drawable/bg_btn_selected"/>
    <item android:drawable="@drawable/bg_btn_normal"/>
</selector>
```
来看bg_btn_pressed，bg_btn_selected它们是一样的
```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">

    <!--灰色背景-->
    <item>
        <shape>
            <solid android:color="#c4c4c4"/>
            <corners android:radius="12dp"/>
        </shape>
    </item>

    <!--蓝色前景-->
    <item android:bottom="6dp"
        android:right="6dp">
        <shape>
            <solid android:color="@android:color/holo_blue_dark"/>
            <corners android:radius="12dp"/>
        </shape>
    </item>
</layer-list>
```
layer-list作为根节点，也可以作为selector中item的子节点。layer-list可以添加多个item子节点，每个item子节点对应一个drawable资源，按照item从上到下的顺序叠加在一起，再通过设置每个item的偏移量就可以看到阴影效果了。
layer-list的item可以通过下面四个属性设置偏移量：
- android:top 顶部的偏移量
- android:bottom 底部的偏移量
- android:left 左边的偏移量
- android:right 右边的偏移量

这四个偏移量和控件的margin设置差不多，都是外间距的效果。如何不设置偏移量，前面的图层就完全挡住了后面的图层，从而也看不到后面的图层效果了。

![](http://upload-images.jianshu.io/upload_images/5734256-0890cea4399b346e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后是bg_btn_normal.xml
```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">

    <!--透明背景-->
    <item>
        <shape>
            <solid android:color="@android:color/transparent"/>
            <corners android:radius="12dp"/>
        </shape>
    </item>

    <!--灰色背景-->
    <item android:bottom="3dp"
        android:right="3dp">
        <shape>
            <solid android:color="#bebebe"/>
            <corners android:radius="12dp"/>
        </shape>
    </item>

    <!--蓝色前景-->
    <item android:bottom="6dp"
          android:right="6dp">
        <shape>
            <solid android:color="@android:color/holo_blue_light"/>
            <corners android:radius="12dp"/>
        </shape>
    </item>
</layer-list>
```
第一个item是一个透明的shape，再在它上面加阴影效果，应为直接加阴影的话，normal状态时button的大小要比按压时大，看起来不太好。这里保证蓝色前景也就是按钮大小不变的情况下，按压时将透明shape那部分变成阴影，并且阴影变淡，这就达到效果了

![](http://upload-images.jianshu.io/upload_images/5734256-e1156d59fea1b673.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##4. drawable
除了上面讲的shape,selector,layer-list，Android还有很多其他drawable资源
###4.1 普通图片
图片格式可以是png(最常用，推荐使用)、jpg、gif，用图片资源需要根据不同屏幕密度提供多张不同尺寸的图片：

密度分类  |  密度值范围  |  代表分辨率  |  图标尺寸  |
--------------|------------------|------------------|----------------|
mdpi         |120~160dpi  |320x480px    |48x48px	  |
hdpi	         |160~240dpi	|480x800px	|72x72px	|
xhdpi	 |240~320dpi	|720x1280px	|96x96px	|
xxhdpi	 |320~480dpi	|1080x1920px	|144x144px|	
xxxhdpi	 |480~640dpi	|1440x2560px	|192x192px|	
一张图片一般需要提供5张不同比例的图片，切图的话可以用下面这个网站在线切
http://icon.wuruihong.com/
Android Studio中也可以用Image Assetl来导入符合Materical Design设计的应用常用图标


![](http://upload-images.jianshu.io/upload_images/5734256-575864176560c7ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

###4.2 Vector Drawable
普通图片需要提供多张不同尺寸的图片，这会加大安装包的大小。而从Android 5.0开始支持的Vector可以自动进行适配，不需要通过分辨率来设置不同的图片，可以大幅减少图像的体积，同样一张图，用Vector来实现，可能只有PNG的几十分之一

利用Android Studio的Vector Asset，可以非常方便的创建Vector图像，也可以直接通过本地的SVG图像来生成Vector图像

![](http://upload-images.jianshu.io/upload_images/5734256-b6885a0bab5eb75c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](http://upload-images.jianshu.io/upload_images/5734256-9ca6a32c2724cd05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

### 4.3 level-list
level-list管理一组drawable，每个drawable设置一组level范围，最终会根据level值选取对应的drawable绘制出来。level-list通过添加item子标签来添加相应的drawable，其下的item只有三个属性：
- android:drawable 指定drawable资源，如果不设置该属性，也可以定义drawable类型的子标签
- android:minLevel 该item的最小level值
- android:maxLevel 该item的最大level值

用它来实现比如电量，信号强弱显示
```
<?xml version="1.0" encoding="utf-8"?>
<level-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:maxLevel="100" android:minLevel="100"
          android:drawable="@drawable/ic_battery_full_black_24dp"/>
    <item android:maxLevel="99" android:minLevel="90"
          android:drawable="@drawable/ic_battery_90_black_24dp"/>
    <item android:maxLevel="89" android:minLevel="80"
          android:drawable="@drawable/ic_battery_80_black_24dp"/>
    <item android:maxLevel="79" android:minLevel="60"
          android:drawable="@drawable/ic_battery_60_black_24dp"/>
    <item android:maxLevel="59" android:minLevel="50"
          android:drawable="@drawable/ic_battery_50_black_24dp"/>
    <item android:maxLevel="49" android:minLevel="30"
          android:drawable="@drawable/ic_battery_30_black_24dp"/>
    <item android:maxLevel="29" android:minLevel="20"
          android:drawable="@drawable/ic_battery_20_black_24dp"/>
    <item android:maxLevel="19" android:minLevel="0"
        android:drawable="@drawable/ic_battery_alert_black_24dp"/>
</level-list>
```
设置给ImageView
```
<ImageView
        android:id="@+id/iv_battery"
        android:layout_width="64dp"
        android:layout_height="64dp"
        android:src="@drawable/battery_level"/>
```
代码中通过getDrawable().setLevel(level)设置level，这里每点一下减少10%电量
```
level-=10;
tvbattery.setText(level+"");
ivbattery.getDrawable().setLevel(level);
```


![](http://upload-images.jianshu.io/upload_images/5734256-54f6b57e0fa24da0.gif?imageMogr2/auto-orient/strip)


item的匹配规则是从上到下的，当设置的level值与前面的item的level范围匹配，则采用。当从大到小排序下来，可以去掉每个item的android:maxLevel属性，比如上面，从小到大则可以去掉android:minLevel

###4.4 animation-list
animation-list可以将一系列drawable构建成帧动画，就是将一个个drawable，一帧一帧的播放。通过添加item子标签设置每一帧使用的drawable资源，以及每一帧持续的时间。
```
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
    <item android:drawable="@drawable/ic_wifi_signal_0" android:duration="1000"/>
    <item android:drawable="@drawable/ic_wifi_signal_1" android:duration="1000"/>
    <item android:drawable="@drawable/ic_wifi_signal_2" android:duration="1000"/>
    <item android:drawable="@drawable/ic_wifi_signal_3" android:duration="1000"/>
    <item android:drawable="@drawable/ic_wifi_signal_4" android:duration="1000"/>
</animation-list>
```
- android:oneshot属性设置是否循环播放，设为true时，只播放一轮就结束，设为false时，则会轮询播放。
- android:duration属性设置该帧持续的时间，以毫秒数为单位。
设置给ImageView，在代码中
```
        ((AnimationDrawable)iv.getDrawable()).start();
```

![](http://upload-images.jianshu.io/upload_images/5734256-64727c525e24f384.gif?imageMogr2/auto-orient/strip)

animation-list对应的Drawable类为AnimationDrawable，要让动画运行起来，需要主动调用AnimationDrawable的start()方法。另外，如果在Activity的onCreate()方法里直接调用start()方法会没有效果，因为view还没有初始化完成是播放不了动画的。

###4.5 
animated-rotate会让drawable不停地做旋转动画（不用在代码中启动）
animated-rotate可设置的属性只有四个：
- android:drawable 指定drawable资源，如果不设置该属性，也可以定义drawable类型的子标签
- android:pivotX 旋转中心的X坐标
- android:pivotY 旋转中心的Y坐标
```
<?xml version="1.0" encoding="utf-8"?>
<animated-rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/bg_progressv"
    android:pivotX="50%"
    android:pivotY="50%">
</animated-rotate>
```
**注意**：50%不能写成0.5, 它们是不同的意思，50%是相对于控件，0.5是相对于整个屏幕

![](http://upload-images.jianshu.io/upload_images/5734256-bd52a45fa5686d5f.gif?imageMogr2/auto-orient/strip)

##5. View Animation
未完
