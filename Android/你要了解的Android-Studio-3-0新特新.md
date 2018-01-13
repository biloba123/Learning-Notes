Android Studio 3.0稳定版刚发布，它相对于2.3版本有较大的优化和新功能，变得更加强大。那来看看都有哪些：

（还没更新的去官网https://developer.android.google.cn/studio/index.html下载更新）

## Android Plugin for Gradle 3.0.0

### 1. 升级插件

在build.gradle文件中更改

```
buildscript {
    repositories {
        // Gradle 4.1 and higher include support for Google's Maven repo using
        // the google() method. And you need to include this repo to download
        // Android plugin 3.0.0 or higher.
        google()
        ...
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.0'
    }
}
```

然后进行同步，这个过程有点慢，并且需要科学上网。完成后对应gradle也更新到4.1

gradle-wrapper.properties

```
...
distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip
```

### 2. 新版本特性

Android Plugin for Gradle 3.0.0主要提高了包含大量模块的项目的构建性能。在这些这些大型项目使用新插件可以更快的构建配置时间，对代码或资源应用简单的更改时，更快的增量构建时间。

除此之外还包含以下内容：

- 支持Android 8.0。
- 支持基于语言资源构建单独的APK。
- 支持Java 8库和Java 8语言功能（不用像之前使用Jack编译器）。
- 支持Android测试支持库1.0（Android测试实用程序和 Android测试协调器。
- 改进了ndk-build和cmake构建速度。
- 改进了渐变同步速度。
- AAPT2默认启用。
- 使用`ndkCompile`更受限制，应该转而使用CMake或ndk-build来编译您要将其打包到APK中的本地代码。



## Kotlin支持

自从I/O大会宣布将Kotlin作为Android指定开发语言，Kotlin就变得很火。3.0也对Kotlin提供了完全的支持

- 将Java文件转换为Kotlin
![](http://upload-images.jianshu.io/upload_images/5734256-e92ae670d89a94a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

便能将Java代码转换为Kotlin，不过转换后有些不正确的地方需要自行修改下

![](http://upload-images.jianshu.io/upload_images/5734256-229431269d56f108.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

- 直接添加Kotlin文件

![](http://upload-images.jianshu.io/upload_images/5734256-5289b6496df43e6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Activity等也支持直接添加Kotlin的模板

![](http://upload-images.jianshu.io/upload_images/5734256-82b25276e3308de7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

默认情况下，Kotlin和Java文件都会放在`src/main/java`目录下，如果你想把它们分开来，可以把Kotlin放在`src/main/kotlin`，并在sourceSets中添加以下配置
```
android {
   sourceSets {
       main.java.srcDirs += 'src/main/kotlin'
   }
}
```

## Android Profiler
新的Android Profiler 取代了Android监视器工具，并提供了一套新的工具来实时测量应用程序的CPU，内存和网络使用情况。
点击工具栏的图标开启

![](http://upload-images.jianshu.io/upload_images/5734256-f17dc71d486ee594.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/5734256-3acbb13ee03cb4f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从Android Profiler的概述时间轴上，单击CPU，MEMORY或 NETWORK时间线以访问相应的分析器工具。

### 1. CPU Profiler
CPU Profiler可帮助你通过触发示例或仪表化的CPU跟踪来分析应用程序的CPU线程使用情况。可以使用各种数据视图和过滤器来解决CPU性能问题。
具体使用[CPU Profiler指南](https://developer.android.com/studio/profile/cpu-profiler.html?hl=zh-cn)


![](http://upload-images.jianshu.io/upload_images/5734256-1cba20678866ba8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2. 内存分析器
内存分析器可以帮助你识别内存泄漏和内存流失，从而导致静音，冻结甚至应用程序崩溃。它显示了应用程序内存使用的实时图形，可以捕获堆转储，强制垃圾收集和跟踪内存分配。
具体使用[内存分析器指南](https://developer.android.com/studio/profile/memeory-profiler.html?hl=zh-cn)

![](http://upload-images.jianshu.io/upload_images/5734256-63bea8ddd2f203f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3. 网络分析器
网络分析器允许您监视应用程序的网络活动，检查每个网络请求的有效负载，并链接到生成网络请求的代码。
具体使用[网络分析器指南](https://developer.android.com/studio/profile/network-profiler.html?hl=zh-cn)

![](http://upload-images.jianshu.io/upload_images/5734256-3b3d28704a7fa811.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## APK剖析和调试
Android Studio现在允许对任何APK进行配置和调试

![](http://upload-images.jianshu.io/upload_images/5734256-1b9ede067a1470e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
或在welcome中选择
![](http://upload-images.jianshu.io/upload_images/5734256-8f98954f68fa5abf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但它不会反编译代码，代码是smail格式的，要正确添加断点和查看堆栈跟踪，需要附加Java源文件和本机调试符号。

![](http://upload-images.jianshu.io/upload_images/5734256-cddd64ae635aedac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 设备文件浏览器
设备文件资源管理器允许你检查连接的设备的文件系统，并在设备和计算机之间传输文件。这将替代DDMS中可用的文件系统工具。
在**View > Tool Windows > Device File Explorer**中打开

![](http://upload-images.jianshu.io/upload_images/5734256-9286554e9a901022.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 自适应图标向导
Image Asset可以为Android8.0创建自适应启动器图标，同时为旧设备创建传统图标（必须设置compileSdkVersion为26或更高才能使用自适应启动器图标。）

![](http://upload-images.jianshu.io/upload_images/5734256-3426f2fd016fdf99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 即时应用支持
Android Instant App能让用户免安装体验你的APP，在项目里可以将某些模块用它来实现。
首先要求在SDK Manager中安装Instant Apps开发SDK

![](http://upload-images.jianshu.io/upload_images/5734256-123b7cd3e99f3870.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](http://upload-images.jianshu.io/upload_images/5734256-03932f3345fc7c46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 其他
### Android Things模块
新建项目和新建模块向导中的新Android Things模板可帮助开发适用于Android的IOT设备。

### 支持字体资源
为了支持Android 8.0中的新字体资源，Android Studio包含一个字体资源选择器，用于将 字体捆绑到应用程序中或配置项目以下载设备上的字体（如果可用）。布局编辑器还可以预览布局中的字体。

要尝试下载字体，请确保您的设备或模拟器正在运行Google Play Services v11.2.63或更高版本。（国内受限...）

![](http://upload-images.jianshu.io/upload_images/5734256-817fdf98f336a7da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 布局编辑器
更新了一些增强功能，包括以下内容：
- 新的工具栏布局和图标。
- 在组件树中更新布局。
- 改进的拖放视图插入。
- 在编辑器下面显示新的错误面板，显示修复建议的所有问题（如果可用）。
- 用于ConstraintLayout的各种UI增强功能
