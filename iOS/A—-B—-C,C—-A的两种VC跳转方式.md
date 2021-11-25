
有A到B再到C，我现在再C页面返回的时候我想直接跳转到A页面 :

1.A push B push C,C pop A;
![push.png](http://upload-images.jianshu.io/upload_images/2051176-9250871ec34d3b03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于push比较好的一点，就是有个栈顶控制器navigationController，它装载所有下面的控制器，从viewControllers里面取出A,连续pop2次就可以回到A控制器：

```
NSArray * viewControllers = weakSelf.navigationController.viewControllers;
    
    for (UIViewController * avc in viewControllers) {
        
        if ([avc isKindOfClass:[UIViewAControllerA class]]) {
            
            [self.navigationController popToViewController:(UIViewController*)avc animated:YES];
        }
    }
```

2. A present B present C,C dismiss A


![present.png](http://upload-images.jianshu.io/upload_images/2051176-9277c09266f73078.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最简单的方式直接使用通知，在C中dismiss的时候发出通知，在B中监听C然后dismiss到A:

```
// 在C中关掉自己
    [self dismissViewControllerAnimated:YES completion:^(){
        //关掉注册controller
        [[NSNotificationCenter defaultCenter] postNotificationName:NOTIFICATION_CLOSE_B object:nil userInfo:nil];
    }];

// 在B中添加监视通知
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(close) name:NOTIFICATION_CLOSE_B object:nil];

-(void) close{
 [self dismissViewControllerAnimated:YES completion:nil];
}

// 移除通知
- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```


