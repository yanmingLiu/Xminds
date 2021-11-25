###1.创建仓库

![QQ20170112-0.png](http://upload-images.jianshu.io/upload_images/2051176-8e221290bc62387a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注意选上README 忽略文件根据语言选择

![QQ20170112-1.png](http://upload-images.jianshu.io/upload_images/2051176-3c3c1a3f4b86d2ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>创建好以后：

![QQ20170112-2.png](http://upload-images.jianshu.io/upload_images/2051176-61e7f6ecb1e247c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###2.配置SSH keys

隐藏文件显示：defaults write com.apple.finder AppleShowAllFiles -bool true  
隐藏文件关闭：defaults write com.apple.finder AppleShowAllFiles -bool true  
重启Finder就可显示隐藏文件
前往个人文件查看有没有 .ssh 文件夹，有的话建议删除，重新配置

![QQ20170112-3.png](http://upload-images.jianshu.io/upload_images/2051176-5cb2e0ce34839cf7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


>终端里输入指令：mkdir .ssh  创建一个 .ssh 文件夹
cd .ssh  进入刚创建的 .ssh文件夹目录里  
输入指令：ssh-Keygen -t rsa -C "你的github邮箱地址" 
输入完成之后一直按回车键 中间会提示你要输入密码，不用管一直按回车。

![QQ20170112-4.png](http://upload-images.jianshu.io/upload_images/2051176-cc325c995ed67b63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>接着输入：ls -la 查看 出现下面界面就配置成功


![QQ20170112-19.png](http://upload-images.jianshu.io/upload_images/2051176-55384392e7a45414.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>输入指令：pbcopy < ~/.ssh/id_rsa.pub  拷贝 或者直接在终端里面拷贝

###3.到github上填写ssh
![Uploading QQ20170112-12_081066.png . . .]](http://upload-images.jianshu.io/upload_images/2051176-91247e66ec1fdfd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![QQ20170112-12.png](http://upload-images.jianshu.io/upload_images/2051176-e322b450602ee2ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>添加完成后

![QQ20170112-13.png](http://upload-images.jianshu.io/upload_images/2051176-ae65fa724b8756a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>到终端输入指令：ssh -T git@github.com  
执行完这条指令之后会输出  Are you sure you want to continue connecting (yes/no)?  
输入 yes 回车 输出：Hi yanmingLiu! You've successfully authenticated, but GitHub does not provide shell access.
到github，刷新网页就可以了。
![QQ20170112-14.png](http://upload-images.jianshu.io/upload_images/2051176-16ae364a95d490b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###4.上传本地代码到github
直接使用github客户端，不解释。 下载 https://desktop.github.com
下载好之后输入用户名 邮箱 密码登陆之后clone刚创建的仓库到本地
![QQ20170112-15.png](http://upload-images.jianshu.io/upload_images/2051176-ddce4322f7ae5ffd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>打开clone到本地的文件夹 将需要上传的项目直接拖进去

![QQ20170112-16.png](http://upload-images.jianshu.io/upload_images/2051176-1b6900706b83d527.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


本地使用github时,commit添加到路径中的项目时会提示Github:failed to add file / to index
是因为创建的项目目录里面有个有一个隐藏的文件”.Git”,删除即可

![error.png](http://upload-images.jianshu.io/upload_images/2051176-3246ea6fc9734a83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>回到github客户端 刷新一下 最新的代码就来了，commit之后Sync github上就上传成功了

![QQ20170112-17.png](http://upload-images.jianshu.io/upload_images/2051176-e4707443dbbb9cc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![QQ20170112-18.png](http://upload-images.jianshu.io/upload_images/2051176-221710d86dd69512.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>实验demo地址
https://github.com/yanmingLiu/YMPickView.git
