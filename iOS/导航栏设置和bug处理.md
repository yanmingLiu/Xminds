直接上葵花宝典

###一、先来bug
1.首页导航栏需要隐藏，二级页面需要导航栏，在首页viewWillAppear设置navigationBar.hidden = YES; viewWillDisappear再改为NO，之后在二级界面手势返回的时候，导航栏Bug如图

![navBug1.png](http://upload-images.jianshu.io/upload_images/2051176-fad2660677de069a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.处理办法在viewWillAppear这类方法animated这个在给参数的时候不要给YES或NO, 直接使用animated。  animated在viewWillAppear这类方法中才能使用, 因为是形参

![navbug4.png](http://upload-images.jianshu.io/upload_images/2051176-ee98f24d5b2f0944.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###二、再来基础设置
####1.全局设置
```
/**
 *  全局设置,1.可以直接放到AppDelegate里面。2.直接放到自定义的NavigationController里面
 */
+ (void)initialize {
    [super initialize];
    
    // 模型修改属性只能用字典
    NSMutableDictionary *dic = [NSMutableDictionary dictionary];
    dic[NSForegroundColorAttributeName] = [UIColor whiteColor];
    
    // 设置返回按钮的颜色
    [[UINavigationBar appearance] setTintColor:[UIColor whiteColor]];
    // 导航栏背景颜色
    [[UINavigationBar appearance] setBarTintColor:[UIColor colorWithRed:0.141 green:0.435 blue:0.698 alpha:1.000]];
    // 导航栏半透明效果 -- 这里需要注意 遇到向上偏移64的的问题 = NO 
    [[UINavigationBar appearance] setTranslucent:YES];
    // 导航栏标题颜色
    [[UINavigationBar appearance] setTitleTextAttributes:dic];
    
    //去掉导航条返回键带的title  这个方法在iOS11出现返回按钮向下偏移的情况
    // [[UIBarButtonItem appearance] setBackButtonTitlePositionAdjustment:UIOffsetMake(0, -60) forBarMetrics:UIBarMetricsDefault];

//去掉导航条返回键带的title
    [[UIBarButtonItem appearance] setBackButtonTitlePositionAdjustment:UIOffsetMake(-100, 0) forBarMetrics:UIBarMetricsDefault];
    
    
    // 使用自己的图片替换原来的返回图片
    [UINavigationBar appearance].backIndicatorImage = backImageN;
    [UINavigationBar appearance].backIndicatorTransitionMaskImage = backImageH;
    
    // 去掉导航栏黑线
    [UINavigationBar appearance].barStyle = UIBarStyleBlackTranslucent;
    [[UINavigationBar appearance] setShadowImage:[UIImage new]];
    // or
    [[UINavigationBar appearance] setBackgroundImage:[UIImage imageWithColor:[self colorFromHexRGB:@"33cccc"]]
                       forBarPosition:UIBarPositionAny
                           barMetrics:UIBarMetricsDefault];
    [[UINavigationBar appearance] setShadowImage:[UIImage new]];
    
    
    //自定义返回按钮
    UIImage *backImageN = [[UIImage imageWithOriginalName:@"back_icon"] resizableImageWithCapInsets:UIEdgeInsetsMake(0, 30, 0, 0)];
    UIImage *backImageH = [[UIImage imageWithOriginalName:@"back_icon_H"] resizableImageWithCapInsets:UIEdgeInsetsMake(0, 30, 0, 0)];
    
}
```

####2.自定义导航栏类
```
- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated {
    if (self.viewControllers.count > 0) { 
        // 二级页面隐藏tabBar
        viewController.hidesBottomBarWhenPushed = YES;
       // 自定义返回按钮
        UIBarButtonItem *leftItem = [UIBarButtonItem itemWithTarget:self action:@selector(back) image:@"nav_return" highImage:@"nav_return"];
        // 设置宽度可以改变返回按钮距离左边屏幕的距离
        leftItem.customView.width = 100;
        viewController.navigationItem.leftBarButtonItem = leftItem;
     // 设置导航栏背景图片
        [self.navigationBar setBackgroundImage:[UIImage imageNamed:@"samuel"] forBarMetrics:UIBarMetricsCompact];

    }
    [super pushViewController:viewController animated:animated];
}

///处理右滑返回手势自定义失效
- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer{ 
    if (self.childViewControllers.count == 1) { 
        return NO; 
    } 
    return YES;
}

- (void)back {
    [self popViewControllerAnimated:YES];
}
```
3.现在主流的上滑动态添加导航栏的背景色, 我使用的是LTNavigationBar
```
/**
 动态修改导航栏颜色
 */
- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    UIColor * color = main_greenColor;
    CGFloat offsetY = scrollView.contentOffset.y;
    if (offsetY > NAVBAR_CHANGE_POINT) {
        CGFloat alpha = MIN(1, 1 - ((NAVBAR_CHANGE_POINT + 64 - offsetY) / 64));
        [self.navigationController.navigationBar lt_setBackgroundColor:[color colorWithAlphaComponent:alpha]];
    } else {
        [self.navigationController.navigationBar lt_setBackgroundColor:[color colorWithAlphaComponent:0]];
    }
}
```
