## Git基本配置

Git配置使用git config分三种，存放在不同位置

文件              |    作用
------------------|--------------------------------------
/etc/gitconfig | 系统中对所有的用户都适用的配置。git config --system ...
~/.gitconfig   |用户目录下的配置文件只适用该用户。git config --global ...
当前项目下的.git/config|配置只对当前项目有效。git config ...

首先要配置用户名和邮箱。每次git提交都会使用这里配置的默认信息
```
git config --global user.name "yourname"
git config --global user.email ...@example.com
```
配置默认文本编辑器，例如emacs
```
git config --global core.editor emacs
```
配置解决差异冲突时使用哪种分析工具，例如vimdiff
```
git config --global merge.tool vimdiff
```
检查已有配置
```
git config --list
```

![](http://upload-images.jianshu.io/upload_images/5734256-b4a893e15bc35ffe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

高亮显示一些配置
```
git config --global color.status auto    #查看状态时高亮
git config --global color.branch auto     #分支名高亮
git config --global color.ui auto     #自动高亮
```
配置别名来简化一些命令，例如checkout 简化为co，status简化为st
```
git config --global alias.co checkout
git config --global alias.st status
```
之后使用命令git checkout 时就可以简化为 git co

## Git基本命令
环境配置好了后就可以使用了，常用的git命令就几个不用担心
### 创建git仓库--git inite
在项目根目录下执行git inite命令（在跟目录下右键Git Bash），会在根目录下创建隐藏.Git目录，包含相关配置和信息

![](http://upload-images.jianshu.io/upload_images/5734256-9f18d0138b9cb20e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 查看本地仓库的状态--git status
使用git status或之前简化的git st，状态有三种已修改，已暂存，已修改。已修改是在工作目录也就是项目里修改还有提交保存；已暂存是指用git add命令提交到要保存的清单（暂存区域）；已提交是暂存区的文件通过git commit提交到本地git仓库；
由于我们新建的仓库，所有文件都没保存提交，都是已修改状态

![](http://upload-images.jianshu.io/upload_images/5734256-4a5e89f9c28039df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 提交修改到暂存区域--git add
通过上面提示可以知道用git add提交，它完整命令参数复杂，只说最常见的两种。一种是添加某个文件，例如app文件
```
git add app
```

![](http://upload-images.jianshu.io/upload_images/5734256-83c3064a82380322.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

提交目录下所有文件
```
git add .
```
在看看现在状态

![](http://upload-images.jianshu.io/upload_images/5734256-2b5398db78183f8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

已变成暂存状态，并且提示可以使用git rm --cached命令将文件从追踪列表移除

### 提交到本地仓库--git commit
使用git commit将追踪列表中文件提交
```
git commit -m "提交说明"         #形式1：通过-m提交简短信息
git commit              #形式2：会跳转编辑器，在提交信息有一定格式或文字较多时用
```

![](http://upload-images.jianshu.io/upload_images/5734256-f55edf7ee0eeac27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在工作区是干净的

![](http://upload-images.jianshu.io/upload_images/5734256-5fe12bd294950c62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 查看项目历史记录--git log
```
git log
```

![](http://upload-images.jianshu.io/upload_images/5734256-abefe9a3826389b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 下载远程项目--git clone
当想下载网络上别人的仓库时，可使用git clone "地址"，例如下载我github上一个EmptyHand项目
```
git clone https://github.com/biloba123/EmptyHand.git
```

![](http://upload-images.jianshu.io/upload_images/5734256-0a38fe1c12e006d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

会在当前目录下新建一个EmptyHand文件存放

### 不同分支--git branch
在上面的操作都显示master，因为默认创建一个master的主分支，不同分支互不影响，当我们开发新功能时一般会新建一个分支在上面操作，因为新加功能可能会有问题最后砍掉这个功能，只有测试通过没有问题再把它合并到master分支上。
例如现在开发热点功能，新建一个hotspot分支
```
git branch hotspot
```

![](http://upload-images.jianshu.io/upload_images/5734256-db474e33f7795e3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看到现在一共两个分支，当前处于master

### 切换分支--git checkout
checkout命令用于签出一个分支或者一个路径。常用于分支切换和恢复文件。列入切换到hotspot分支
```
git checkout hotspot
```
![](http://upload-images.jianshu.io/upload_images/5734256-9b836964c5cef28c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在已经在hotspot分支，我们开发啊开发（几天过去了...），功能差不多实现了将代码保存提交

![](http://upload-images.jianshu.io/upload_images/5734256-51057e60a1a185f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然而，hotspot出现严重bug，只放弃它
```
git checkout master    #切换回master
git branch -D hotspot     #删除hotspot分支
```

![](http://upload-images.jianshu.io/upload_images/5734256-a64f9ea385458c5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

checkout的另一个常用功能是将文件恢复到修改之前状态。比如今天去在ConnectHotspotFragment.java文件里加了好多好多代码，然而写完发现完全不能用...，而且由于太乱之前代码也不能用了。。。
别担心通过下面命令可以恢复
```
git checkout -- ConnectHotspotFragment.java
```

### 合并分支--git merge
如果hotspot开发完成没bug，要将它合并到master分支上。首先提交hotspot代码，切换到主分支，再执行git merge hotspot命令，如果没冲突就能直接合并

![](http://upload-images.jianshu.io/upload_images/5734256-d9ed7e2e3e0123cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 解决冲突
合并并不能经常一帆风顺，比如我现在主要负责这个项目，然后有另一个人协助我，我就把hotspot丢给他做，我也继续在主分支上做，但如果我们同时改了MainActivity，那在合并时

![](http://upload-images.jianshu.io/upload_images/5734256-bd0b824de7ccf3a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看到代码里

![](http://upload-images.jianshu.io/upload_images/5734256-80f50bf5b2de5f49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

两个分支同时改了这里，所以git不知道该用谁的，这时就要手动合并

![](http://upload-images.jianshu.io/upload_images/5734256-b0b0262c2641ed70.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后再git add和commit提交代码即可

### 为版本打个标签--git tag
当完成所有功能并通过测试时，通常会为这个版本打个标签
```
git tag -a v1.0 -m "相关信息"
```

![](http://upload-images.jianshu.io/upload_images/5734256-1249a97b056b8b1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也可以通过" git tag -d 标签名 "来删除tag，通过" git show 标签名"查看版本具体信息

![](http://upload-images.jianshu.io/upload_images/5734256-e566fbc1c9b60a75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 帮助文档--git help
当你不知道某个命令怎么用时，可以通过 git help 来获取其完整说明。例如查看tag命令说明
```
git help tag
```

![](http://upload-images.jianshu.io/upload_images/5734256-b21472909e8ef3e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)






