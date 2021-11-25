本文直接开干性，需要知道的原理的简书很多文章都写的很详细，自行搜索。

###一、iOS9 以前只需要设置
step1.修改plist文件View controller-based status bar appearance = NO
![iso9.png](http://upload-images.jianshu.io/upload_images/2051176-e4def8e49c402ffb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
step2.
```
[[UIApplication sharedApplication] setStatusBarStyle:UIStatusBarStyleLightContent];
```
###二、iOS9 以后，苹果废弃了以上方法，改用- (UIStatusBarStyle)preferredStatusBarStyle

step1.修改plist文件View controller-based status bar appearance = YES
step2.在需要修改的VC里面，直接调用preferredStatusBarStyle
```
-(UIStatusBarStyle)preferredStatusBarStyle{
    return UIStatusBarStyleLightContent;
}
```
###三、如果需要全局设置
step1.修改plist文件View controller-based status bar appearance = YES
step2.搞一个baseVC，调用preferredStatusBarStyle，全局的状态都改了，其他VC继承不继承baseVC都没有关系。

****注意：-->由此可得出，
1>只要在一个vc里面调用了preferredStatusBarStyle，那所有的vc都改了，
2>最先显示到windon上的vc调用了preferredStatusBarStyle，如果返回了白色，那其他的vc就算调用也不会改变，还是白色。

###四、大部分状态栏颜色一致，部分不一样的设置
step1.修改plist文件View controller-based status bar appearance = YES
setp2.在最先显示到windon上的vc上调用preferredStatusBarStyle，或者是baseVC，返回需要的颜色。
```
-(UIStatusBarStyle)preferredStatusBarStyle{
    return UIStatusBarStyleLightContent;
}

```
setp3.注意这里是关键，每个项目都有自己的自定义UINavigationController类，或者分类；在导航类里面调用：
```
- (UIViewController *)childViewControllerForStatusBarStyle{
    return  self.visibleViewController;
}
- (UIViewController *)childViewControllerForStatusBarHidden{
    return self.visibleViewController;
}
```
setp4.在需要不一样的vc里面再调用preferredStatusBarStyle返回需要的状态栏颜色
```
-(UIStatusBarStyle)preferredStatusBarStyle{
    return UIStatusBarStyleDefault;
}
```
