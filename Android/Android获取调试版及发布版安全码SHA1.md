## 签名文件
签名文件keystore一般有两种：jks后缀或者keystore后缀，Android Studio中创建的为jks后缀，keytool创建的为keystore后缀，两种文件均可用于APP打包。

**开发版SHA1：**是指在开发调试时，从Android Studio或者Eclipse中直接运行的APP在打包时使用了开发工具默认的开发版（debug）签名文件，开发版签名文件对应有唯一的SHA1。

**发布版SHA1：**是指在提交测试或发布时，APP签名打包过程中选择的指定签名文件，一般此签名文件为开发者或者开发团队持有，与具体的开发工具无关，发布版签名文件同样也对应有唯一的SHA1。

## Android获取SHA1
在AndroidStudio中的Terminal中使用keytool命令获取。
### 发布版SHA1
```
keytool -exportcert -list -v -alias <your-key-name> -keystore <path-to-production-keystore>
```
**<your-key-name>：**发布版签名文件中key的别名（非必需，如果在创建时没设别名可以去掉-alias <your-key-name>）
**<path-to-production-keystore>：**替换为自己发布版签名文件的完整路径

例如：
keystore放在 C:\android_projects\biloba.jks
```
keytool -exportcert -list -v -keystore C:\android_projects\biloba.jks
```

![](http://upload-images.jianshu.io/upload_images/5734256-5a13ca3fd7b716bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

若为keystore类型的文件只用把后缀名换一下。

### 调试版SHA1
调试版keystore默认在 C:\User\.android\debug.keystore（Windows下），
若为Mac和Linux，此路径应该为~/.android/debug.keystore
**keystore的密码默认为android**

![](http://upload-images.jianshu.io/upload_images/5734256-a6d06b1baa4e2462.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

命令和上面一样，只不过把路径换成默认签名文件所在路径
```
keytool -exportcert -list -v -keystore C:\Users\23738\.android\debug.keystore
```

![](http://upload-images.jianshu.io/upload_images/5734256-e0d112f97fbb00d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


