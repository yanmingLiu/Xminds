####1.导航栏translucent属性

>这个BOOL属性能控制UITabBar/UINavigationBar的半透明效果，默认为YES.

>UINavigationBar/UITabBar的translucent属性官方解释：
默认为YES，可以通过设置NO来强制使用非透明背景，
如果导航条使用自定义背景图片，那么默认情况该属性的值由图片的alpha（透明度）决定，如果alpha的透明度小于1.0值为YES。
如果手动设置translucent为YES并且使用自定义不透明图片，那么会自动设置系统透明度（小于1.0）在这个图片上。
如果手动设置translucent为NO并且使用自定义带透明度（透明度小于0）的图片，那么系统会展示这张背景图片，只不过这张图片会使用事先确定的barTintColor进行不透明处理，若barTintColor为空，则会使用UIBarStyleBlack（黑色）或者UIBarStyleDefault（白色）。


**UIScrollView**
translucent = YES，的时候 系统自动为scrollView增加了-顶部64px的内边距-底部49px的内边距，正好是导航栏高度+状态栏高度以及TabBar高度。

translucent = NO，scrollView内容将会被挡住。解决方法：
```
self.automaticallyAdjustsScrollViewInsets = NO，

if (@available(iOS 11.0, *)){
        [[UIScrollView appearance] setContentInsetAdjustmentBehavior:UIScrollViewContentInsetAdjustmentNever];
    }
```

**非UIScrollView**
1.translucent = YES，的时候，视图会向下偏移64，解决办法：translucent = NO
2.既要translucent = YES，也需要正常显示，解决方法：
   2.1手动修改frame布局，从64开始布局，底部同理。
   2.2修改viewController的edgesForExtendedLayout属性，edgesForExtendedLayout = UIRectEdgeNone


**UISearchController**
translucent = NO，在点击搜索，动画之后整个searchBar跑出屏幕外,解决办法：

```
self.navigationController.extendedLayoutIncludesOpaqueBars = YES;
```



>extendedLayoutIncludesOpaqueBars意思是额外布局是否包括不透明的Bar，默认为NO，意味着如果导航条或者TabBar非透明，view内容不会被他们遮挡，如果该属性设置为YES，那么在导航条或者TabBar非透明的情况下，view的内容将会被他们遮挡（原点为0，0），该属性仅仅对非透明的Bar控件有效。
苹果对UIViewController提供了以下几个属性供开发者使用：
```
@property(nonatomic,assign) BOOL wantsFullScreenLayout NS_DEPRECATED_IOS(3_0, 7_0) __TVOS_PROHIBITED; // Deprecated in 7_0, Replaced by the following:

@property(nonatomic,assign) UIRectEdge edgesForExtendedLayout NS_AVAILABLE_IOS(7_0); // Defaults to UIRectEdgeAll

@property(nonatomic,assign) BOOL extendedLayoutIncludesOpaqueBars NS_AVAILABLE_IOS(7_0); // Defaults to NO, but bars are translucent by default on 7_0.  

@property(nonatomic,assign) BOOL automaticallyAdjustsScrollViewInsets API_DEPRECATED_WITH_REPLACEMENT("Use UIScrollView's contentInsetAdjustmentBehavior instead", ios(7.0,11.0),tvos(7.0,11.0)); // Defaults to YES
```


















