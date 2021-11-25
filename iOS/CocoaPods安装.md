一.CocoaPods简介
CocoaPods是一个负责管理iOS项目中第三方开源库的工具。CocoaPods的项目源码在Github上管理。

二.CocoaPods的下载及安装

mac系统已经默认安装好Ruby环境，如果你不确定自己系统中是否有Ruby的，可以在终端中输入命令行：
```
ruby -v    //查看当前ruby版本。
```
但是有时候遇cocoapods-core requires Ruby version >= 2.0.0.的问题，就需要更新ruby

```
// 使用Home-brew安装Ruby
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

brew update //更新brew支持的最新版本

brew install ruby//抓取最新版本并安装

// 如果不能切换到最新的版本
//附加路径的设定
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile

//重新加载设置，并查看版本号
$ source ~/.bash_profile
$ ruby -v

```

###步骤：
1.移除原来的cocoapods
```
sudo gem uninstall cocoapods
```

2.检查ruby源\更换ruby源：
```
gem sources -l  //查看ruby源

gem sources --remove https://rubygems.org/     //删除

//最新ruby镜像如下：
gem source -a https://gems.ruby-china.com/

```

3.安装sudo gem install 苹果系统升级 OS X EL Capitan 后改为
```
sudo gem install -n/usr/local/bin cocoapods  
```
 
4.升级gem：
```
 $ sudo gem update --system
```

5.安装cocoa pods：
```
$ pod setup
```
5.1问题
>setup这一步会很慢或者无限卡 Setting up CocoaPods master repo，
这说明某些环境原因导致pod更新不了，可能原因有，1）gem版本太低；2）github无法链接；3）.cocoapods目录下的配置信息错误。我们可以一个一个来排除，

1>问题
```
/usr/local/Cellar/ruby/2.4.0/lib/ruby/2.4.0/rubygems.rb:270:in `find_spec_for_exe': can't find gem cocoapods (>= 0.a) (Gem::GemNotFoundException)
	from /usr/local/Cellar/ruby/2.4.0/lib/ruby/2.4.0/rubygems.rb:298:in `activate_bin_path'
	from /usr/local/bin/pod:22:in `<main>'
```
解决办法：
```
$ rvm install ruby-2.4.0
$ rvm reinstall ruby-2.4.0
```

出现
```
Error running '__rvm_make -j 1',showing last 15 lines of /Users/GDarkness/.rvm/log/1474100434_ruby-2.4.0/make.log
```

![QQ20170209-102526.png](http://upload-images.jianshu.io/upload_images/2051176-d7c87984f5c4a5b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

先安装xcode command line 即可解决
```
$ xcode-select --install
```
2>然后检查是否可以ping通github，在终端中输入：
```
$ ping github.com 
```
3>然后查看pob repo list：
```
$ pod repo list
```
说明Cocoapods在将它的信息下载到 ~/.cocoapods里；
cd  到该目录里，用du -sh *命令来查看文件大小，结果显示0 repos，说明没有安装成功；
```
cd ~/.cocoapods
du -sh *
```
这是因为要在github下载代码，查询网上都说要将specs仓库镜像换成gitcafe上的镜像或者是 oschina 上的镜像即：
```
$ pod repo remove master
$ pod repo add master https://git.coding.net/CocoaPods/Specs.git
//或者使用
$ pod repo add master https://git.oschina.net/akuandev/Specs.git
```
>但是执行$ pod repo remove master之后老是提示
[!] repo master does not exist
虽后执行$ pod repo add master https://git.coding.net/CocoaPods/Specs.git 之后又会提示
[!] To setup the master specs repo, please run `pod setup`.
然后就无限卡死这两步。

最后直接手动将代码git到本地得以解决问题:
```
$ cd ~/.cocoapods/repos
$ https://github.com/CocoaPods/Specs.git master
 ```
git完成之后执行：
```
$ pod repo update
$ pod repo
```



pod search AFNetworking 输入过后它可能会报：
```
[!] Unable to find a pod with name, author, summary, or descriptionmatching `AFNetworking`
```
解决方案是：
```
rm ~/Library/Caches/CocoaPods/search_index.json
```
后在一次输入：pod search AFNetworking
