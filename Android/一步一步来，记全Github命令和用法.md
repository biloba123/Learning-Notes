## SSH key配置
为了在使用github过程中重复输入用户名，密码，通常需要配置生成ssh key，并把它添加到github上，使得远程命令时github能识别我们的机器，并直接给与授权。
首先在本机生成一个ssh key
```
ssh-keygen -t rsa -b 4096 -C "XXXX@example.com"     #邮箱用github注册时的邮箱
```

![](http://upload-images.jianshu.io/upload_images/5734256-21370905c24e0653.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

会问ssh key存放在哪里，默认路径为"/Users/用户名/.ssh"目录，直接回车
然后会有两个提示要求输入密码，直接回车，就没有密码，这样就生成了id_rsa和id_rsa.pub文件，存放了ssh key，通过它就可以直接访问github不需要任何密码。
复制id_rsa.pub的内容，到你的github账号，点开setting，选择"SSH Keys",点"New SSH key",将生成的key绑定上去

![](http://upload-images.jianshu.io/upload_images/5734256-22d53f274a0b7629.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](http://upload-images.jianshu.io/upload_images/5734256-685b69ab2ed7f850.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过下面命令测试是否添加成功
```
ssh -T git@github.com
```

![](http://upload-images.jianshu.io/upload_images/5734256-3cdca95eb7242c0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

输出以上信息说明操作成功

## 项目托管--git remote
比如本地有个创建好的AutoBR的项目，想把它托管到github上。那第一步需要在github上创建一个仓库

![](http://upload-images.jianshu.io/upload_images/5734256-0f812203f07c2d43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

获取它的地址：git@github.com:biloba123/AutoBR.git，注意地址有两种，一种https形式，另一种ssh格式，我们用ssh这样就不用输密码

![](http://upload-images.jianshu.io/upload_images/5734256-5a4a0b8527642e2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后将本地项目和该仓库关联起来
```
git remote add origin git@github.com:biloba123/AutoBR.git
```
这样就向本地仓库添加了一个名为origin，地址是git@github.com:biloba123/AutoBR.git的远程仓库，通过" git remote -v "来查看该项目的远程仓库（一个本地仓库可以和多个远程仓库相关联）

![](http://upload-images.jianshu.io/upload_images/5734256-64496851421da7e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 将项目推送到远程仓库--git push
关联后，就可以和远程仓库进行数据同步，同步前想把代码提交git commit,推送用到的命令是
```
git push  远程仓库名  本地分支:远程分支
```
例如将本地master同步到origin的master分支上
```
git push origin master:master

#当本地分支和远程一样时也可只写一个
git push origin master
```
删除远程分支，例如删除origin的net分支用下面命令
```
git push origin :net
```

## 更新最新代码--git pull
直接git push把代码推送到github上并不是很好的做法，比如你和别人协作时你的代码并不是最新的，直接同步会覆盖覆盖最新代码，上面推送时就有问题

![](http://upload-images.jianshu.io/upload_images/5734256-84bbc51f8df730bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为远程仓库已经有一些文件，本地项目并没和远程合并，提交被拒绝。首先应该用git pull从github将最新的代码更新到本地，并确保合并成功，如果有冲突，先解决再git push

![](http://upload-images.jianshu.io/upload_images/5734256-0cf58c100668bb52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但是又出现了 fatal: refusing to merge unrelated histories ，这是git 2.9后做了一些更改，在第一次git pull会出问题，用下面命令
```
git pull origin master --allow-unrelated-histories
```
输入合并信息，在git push就可以了

## git ignore忽略文件
有些文件例如apk文件，本机相关配置文件，不需要提交到远程仓库，那就把它们添加到.gitignore文件中

![](http://upload-images.jianshu.io/upload_images/5734256-c6d5c8bb4836910c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
