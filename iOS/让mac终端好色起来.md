##自带终端设置为彩色
切换默认Shell到Zsh

Mac OS X默认已经安装好了Zsh，你可以打开终端，输入zsh --version来确认，如果没有安装，请参考这个文档(https://github.com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH)。

打开终端输入下面的命令，切换默认Shell为Zsh：

chsh -s /bin/zsh

关闭终端重新打开后，你将默认使用zsh作为终端Shell。然而你会发现，终端并没有变得多酷炫，接着往下走，安装Oh My ZSH!

安装Oh My ZSH!

打开终端输入下面的命令：

sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

耐心等待一会儿，然后你就会发现你的终端变成了彩色的了。


![QQ20170122-160220.png](http://upload-images.jianshu.io/upload_images/2051176-1ca08afff31d98cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##替换系统默认终端 -- iTerm 2

如果你经常使用终端，那么推荐你使用iTerm 2来替代系统自带的终端。


