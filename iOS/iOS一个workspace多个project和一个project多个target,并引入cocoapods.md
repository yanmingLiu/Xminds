### A.一个workspace多个project

```
 步骤:
     1.创建一个XCWorkspace.xcworkspace 
     2.在XCWorkspace文件夹下创建多个项目工程 例:Project1 Project2
     3.打开XCWorkspace.xcworkspace,添加project到xcworkspace
     4.创建Podfile文件,编写对应的project引入的第三方库,公用的统一pod
     5.pod install 
```

####1.创建一个XCWorkspace.xcworkspace 
![1.png](http://upload-images.jianshu.io/upload_images/2051176-dbfc3642e8be9bfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####2.在XCWorkspace文件夹下创建多个项目工程 例:Project1 Project2 
![2.png](http://upload-images.jianshu.io/upload_images/2051176-1f02b8c7246985b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####3.打开XCWorkspace.xcworkspace,添加project到xcworkspace
![3-1.png](http://upload-images.jianshu.io/upload_images/2051176-7ccadc40d878ae84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![3-2.png](http://upload-images.jianshu.io/upload_images/2051176-29063addf8981dc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![3-3.png](http://upload-images.jianshu.io/upload_images/2051176-0d71c58424cfa028.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####4.创建Podfile文件,编写对应的project引入的第三方库,公用的统一pod
```
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

workspace 'XCWorkspace.xcworkspace'

project 'Project1/Project1.xcodeproj'
project 'Project2/Project2.xcodeproj'


abstract_target 'CommonPods' do  
    
    #公用pods
    use_frameworks!
    
    pod 'SnapKit'  
   

    #project分别处理

    target 'Project1' do
        platform :ios, '9.0'
        project 'Project1/Project1.xcodeproj'
    end
    
    target 'Project2' do
        platform :ios, '11.0'
        project 'Project2/Project2.xcodeproj'
    end
    
end
```

####5.pod install 之后多个同的project都引入了第三方库, 一个workspace多个project配置就全部完成.
![5.png](http://upload-images.jianshu.io/upload_images/2051176-079820d3de9e764a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


-------------------------------------------------------------------------

### B.一个project多个target

```
 步骤:
 1.添加target:duplicate一个target,同时也会生成一个Project1 copy-Info文件. (duplicate:与原target共用一份代码，可单独设置的编译参数、bundleid、 应用名、Icon等信息。一份代码 多个版本。)
 2.修改target  scheme Info.plist名称和Info.plist文件路径
 3.设置预处理宏,区分不同的target版本。
 4.添加target判断
```

####1.添加target
![b1.png](http://upload-images.jianshu.io/upload_images/2051176-4e501515423accf8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![b2-1.png](http://upload-images.jianshu.io/upload_images/2051176-7f675cae9fbd0f7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.修改target  scheme Info.plist名称和Info.plist文件路径
![b2-2.png](http://upload-images.jianshu.io/upload_images/2051176-e81aa538cd3788e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![b2-3-1.png](http://upload-images.jianshu.io/upload_images/2051176-5587b3e8194ea098.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![b2-3-2.png](http://upload-images.jianshu.io/upload_images/2051176-aeffabe874edb7d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![b2-3-3.png](http://upload-images.jianshu.io/upload_images/2051176-ac941066faa4d4ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.设置预处理宏,区分不同的target版本。

 Swift ，编译器不再支持预处理指令了,这里设置和OC有区别,定义需要添加"-D"。[参考使用 Xcode 的 Target 区分开发和生产环境](http://swift.gg/2016/04/22/using-xcode-targets) 

![b2-8-1.png](http://upload-images.jianshu.io/upload_images/2051176-b9ff2f1e04cdcf9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![b2-8-2.png](http://upload-images.jianshu.io/upload_images/2051176-7443b4aa14c96639.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####4.添加target判断
![b2-9.png](http://upload-images.jianshu.io/upload_images/2051176-4592fb47b7ba4c7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![b2-9-1.png](http://upload-images.jianshu.io/upload_images/2051176-bf868b0a6ebca6c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


