![666.gif](http://upload-images.jianshu.io/upload_images/2051176-e42c54e36057c167.gif?imageMogr2/auto-orient/strip)


###安装 
1.通过Cocoapods安装
          pod 'SVProgressHUD'
2.手动安装
        注意把 SVProgressHUD.bundle 添加进 Targets->Build Phases->Copy Bundle Resources.
        添加 QuartzCore framework 到你的项目.

###使用
```
 SVProgressHUD是单例，使用很简单
[SVProgressHUD show];
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    // time-consuming task
    dispatch_async(dispatch_get_main_queue(), ^{
        [SVProgressHUD dismiss];
    });
});
```

###显示方法
```
+ (void)show;
+ (void)showWithStatus:(NSString*)string;
// 可指定进度
+ (void)showProgress:(CGFloat)progress;
+ (void)showProgress:(CGFloat)progress status:(NSString*)status;

// 提示信息
+ (void)showInfoWithStatus:(NSString *)string;
+ (void)showSuccessWithStatus:(NSString*)string;
+ (void)showErrorWithStatus:(NSString *)string;
+ (void)showImage:(UIImage*)image status:(NSString*)string;
```

###隐藏方法
```
+ (void)dismiss;
+ (void)dismissWithDelay:(NSTimeInterval)delay;
```
###显示多个
应为是单例，想显示多个HUD ，可以使用使用一下方法：
```
+ (void)popActivity;
```

###自定义
```
+ (void)setDefaultStyle:(SVProgressHUDStyle)style;                  // 默认是SVProgressHUDStyleLight
+ (void)setDefaultMaskType:(SVProgressHUDMaskType)maskType;         // 默认是SVProgressHUDMaskTypeNone
+ (void)setDefaultAnimationType:(SVProgressHUDAnimationType)type;   // 默认是 SVProgressHUDAnimationTypeFlat
+ (void)setRingThickness:(CGFloat)width;                            // 默认是 2 pt
+ (void)setCornerRadius:(CGFloat)cornerRadius;                      // 默认是 14 pt
+ (void)setFont:(UIFont*)font;                                      // 默认是 [UIFont preferredFontForTextStyle:UIFontTextStyleSubheadline]
+ (void)setForegroundColor:(UIColor*)color;                         // 默认是 [UIColor blackColor], 仅对 SVProgressHUDStyleCustom 有效
+ (void)setBackgroundColor:(UIColor*)color;                         // 默认是 [UIColor whiteColor], 仅对 SVProgressHUDStyleCustom 有效
+ (void)setInfoImage:(UIImage*)image;                               //默认是bundle文件夹中的提示图片.
+ (void)setSuccessImage:(UIImage*)image;                            // 默认是bundle文件夹中的成功图片.
+ (void)setErrorImage:(UIImage*)image;                              // 默认是bundle文件夹中的错误图片.
+ (void)setViewForExtension:(UIView*)view;                          // 默认是nil,仅当设置了 #define SV_APP_EXTENSIONS 时有效.

```
