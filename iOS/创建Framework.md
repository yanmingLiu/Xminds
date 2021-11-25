库 说白了就是一种可执行代码的二进制格式，可以被载入内存中执行。
什么时候用库？
一种情况是某些代码需要给别人使用，但是我们不希望别人看到源码，就需要以库的形式进行封装，只暴露出头文件。
另外一种情况是，对于某些不会进行大的改动的代码，我们想减少编译的时间，就可以把它打包成库，因为库是已经编译好的二进制了，编译的时候只需要 Link 一下，不会浪费编译时间。

库分静态库和动态库两种。
iOS中的静态库有 .a 和 .framework两种形式；
动态库有.dylib 和 .framework 形式，后来.dylib动态库又被苹果替换成.tbd的形式。
Framework 实际上是一种打包方式，将库的二进制文件，头文件和有关的资源文件打包到一起，方便管理和分发。
 
步骤：
1.创建一个framework

![QQ20170124-093148.png](http://upload-images.jianshu.io/upload_images/2051176-f15b8c12a535db45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.删除默认创建的.h ， info.plist要保留；

![QQ20170124-094114.png](http://upload-images.jianshu.io/upload_images/2051176-4252f0b2f331c096.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.导入自己要打包的源文件，或自己创建

![QQ20170124-094533.png](http://upload-images.jianshu.io/upload_images/2051176-22b26c1b6150a812.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![QQ20170124-104722.png](http://upload-images.jianshu.io/upload_images/2051176-07c9af3aadc898dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.配置项目

![QQ20170124-094838.png](http://upload-images.jianshu.io/upload_images/2051176-1a1342590c3cfe92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![QQ20170124-100032.png](http://upload-images.jianshu.io/upload_images/2051176-4b05d8b63a4459ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![QQ20170124-100140.png](http://upload-images.jianshu.io/upload_images/2051176-727e635ae4d07578.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![QQ20170124-101245.png](http://upload-images.jianshu.io/upload_images/2051176-e7d24225eb655f43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.cmd+b 分别在模拟器可真机下编译，编译完成后可以看到红色的product变成黑色，

![QQ20170124-100459.png](http://upload-images.jianshu.io/upload_images/2051176-d96835ad82cef154.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6.show in finder

![QQ20170124-101934.png](http://upload-images.jianshu.io/upload_images/2051176-05d554d888fffa3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7.合并模拟器版framework和真机版framework
合并的命令和.a稍有不同，不同之处是：framework静态库合并的不是framework,而是framework下的一个二进制文件，即上一步图中标记的文件。
lipo -create 第一个framework下二进制文件的绝对路径 第二个framework下二进制文件的绝对路径 -output 最终的二进制文件路径。

![QQ20170124-102936.png](http://upload-images.jianshu.io/upload_images/2051176-4166bac4670afa9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

8.最后将任何一个framework中的二进制文件替换成合并后的二进制文件即可。
把framework添加到要使用的项目中即可使用。

![QQ20170124-103823.png](http://upload-images.jianshu.io/upload_images/2051176-1b0da416ee40d35c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
