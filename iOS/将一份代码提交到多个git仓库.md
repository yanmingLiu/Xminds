>开发中，可能遇到需要把一个仓库的代码，git历史记录全部copy到另一个仓库，为了同时在两个git仓库中备份代码。

**1.最简单直接的方法就是直接配置.git/config文件**

```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "origin"]
	url = http://    第1个git仓库地址
	url = http://    第2个git仓库地址
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
[branch "dev"]
	remote = origin
	merge = refs/heads/dev
[branch "lym"]
	remote = origin
	merge = refs/heads/lym
```


![提交多个git仓库.png](http://upload-images.jianshu.io/upload_images/2051176-48359fad828558cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**2.只clone某个文件夹**

>通常一个仓库里面的文件都非常多，比如UI资源仓库，除了iOS、安卓、PHP、PSD。。。那我们pull的时候肯定不想一起pull，毕竟磁盘空间有限，所有拉取我们需要的文件或文件夹就好。

步骤：
```
1.连接远程仓库
2.指定克隆模式: 稀疏克隆模式
3.指定克隆的文件夹
4.拉取远程文件

1. 创建本地文件夹 -> 切换目录->初始化-> 指定远程仓库
mkdir gitUI 
cd gitUI     
git init 
git remote add -f origin http://YanmingLiu@git.ykx100.com/tanmengyu/UI.git 

2.指定克隆模式
git config core.sparsecheckout true  

3.指定克隆的文件夹->查看目录
echo '/B端/B端ios切图素材/*' >> .git/info/sparse-checkout 
cat ./.git/info/sparse-checkout   

4. pull
git pull origin  master  //拉取

```

**3. .git文件夹太大, 删除大文件方法**

http://www.cnblogs.com/lout/p/6111739.html
http://blog.csdn.net/zcf1002797280/article/details/50723783
