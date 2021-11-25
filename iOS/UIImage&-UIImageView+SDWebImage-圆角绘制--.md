经常需要我们处理图片，水印、圆角等等，处理圆角我们经常使用layer.cornerRadius，
这样是非常耗性能点做法，为什么耗性能，可以去看如何保持IOS界面流畅的博客，里面有详细讲解。
最好的做法就是异步去绘制图片，具体方法如下：



### .h文件

```
#import <UIKit/UIKit.h>

@interface UIImage (Extension)

/**
 *
 *  @param bgImageName    背景图片
 *  @param waterImageName 水印图片
 *  @param scale 图片生成的比例
 *  @return 添加了水印的背景图片
 */
+(UIImage *)waterImageWithBgImageName:(NSString *)bgImageName waterImageName:(NSString *)waterImageName scale:(CGFloat)scale;

/**
 *
 *  @param imageName    需要裁剪的图片
 *  @param borderColor 边框的颜色
 *  @param borderWidth 边框的宽度
 *  @return 一个裁剪 带有边框的圆形图片
 */
+(UIImage *)circleImageWithImageName:(NSString *)imageName borderColor:(UIColor *)borderColor borderWidth:(CGFloat)borderWidth;

// 根据颜色生成一张尺寸为1*1的相同颜色图片
+ (UIImage *)imageWithColor:(UIColor *)color;


/**
 异步绘制圆角图片

 @param size 图片大小
 @param fillColor 背景颜色
 @param completion 回调
 */
- (void)cornerImageWithSize:(CGSize)size fillColor:(UIColor *)fillColor completion:(void (^)(UIImage *image))completion;


@end
```

### .m文件

```
#import "UIImage+Extension.h"

@implementation UIImage (Extension)

+(UIImage *)waterImageWithBgImageName:(NSString *)bgImageName waterImageName:(NSString *)waterImageName scale:(CGFloat)scale{
    // 生成一张有水印的图片，一定要获取UIImage对象 然后显示在imageView上
    
    //创建一背景图片
    UIImage *bgImage = [UIImage imageNamed:bgImageName];
    //NSLog(@"bgImage Size: %@",NSStringFromCGSize(bgImage.size));
    // 1.创建一个位图【图片】，开启位图上下文
    // size:位图大小
    // opaque: alpha通道 YES:不透明/ NO透明 使用NO,生成的更清析
    // scale 比例 设置0.0为屏幕的比例
    // scale 是用于获取生成图片大小 比如位图大小：20X20 / 生成一张图片：（20 *scale X 20 *scale)
    //NSLog(@"当前屏幕的比例 %f",[UIScreen mainScreen].scale);
    UIGraphicsBeginImageContextWithOptions(bgImage.size, NO, scale);
    
    // 2.画背景图
    [bgImage drawInRect:CGRectMake(0, 0, bgImage.size.width, bgImage.size.height)];
    
    // 3.画水印
    // 算水印的位置和大小
    // 一般会通过一个比例来缩小水印图片
    UIImage *waterImage = [UIImage imageNamed:waterImageName];
    //#warning 水印的比例，根据需求而定
    CGFloat waterScale = 0.4;
    CGFloat waterW = waterImage.size.width * waterScale;
    CGFloat waterH = waterImage.size.height * waterScale;
    CGFloat waterX = bgImage.size.width - waterW;
    CGFloat waterY = bgImage.size.height - waterH;
    
    
    [waterImage drawInRect:CGRectMake(waterX, waterY, waterW, waterH)];
    
    // 4.从位图上下文获取 当前编辑的图片
    UIImage *newImage = UIGraphicsGetImageFromCurrentImageContext();
    
    
    // 5.结束当前位置编辑
    UIGraphicsEndImageContext();
    
    
    return newImage;
}


+(UIImage *)circleImageWithImageName:(NSString *)imageName borderColor:(UIColor *)borderColor borderWidth:(CGFloat)borderWidth{
    
    //需求：从位图上下文，裁剪图片[裁剪成圆形，也添加圆形的边框]，生成一张图片
    
    // 获取要裁剪的图片
    UIImage *img = [UIImage imageNamed:imageName];
    CGRect imgRect = CGRectMake(0, 0, img.size.width, img.size.height);
    
    // 1.开启位图上下文
    UIGraphicsBeginImageContextWithOptions(img.size, NO, 0.0);
    
    //#warning 在自定义的view的drawRect方法里，调用UIGraphicsGetCurrentContext获取的上下文，是图层上下文(Layer Graphics Context)
    // 1.1 获取位图上下文
    CGContextRef bitmapContext = UIGraphicsGetCurrentContext();
    
    // 2.往位图上下裁剪图片
    
    // 2.1 指定一个圆形的路径，把圆形之外的剪切掉
    CGContextAddEllipseInRect(bitmapContext, imgRect);
    CGContextClip(bitmapContext);
    
    
    // 2.2 添加图片
    [img drawInRect:imgRect];
    
    // 2.3 添加边框
    // 设置边框的宽度
    CGContextSetLineWidth(bitmapContext, borderWidth);
    // 设置边框的颜色
    [borderColor set];
    CGContextAddEllipseInRect(bitmapContext, imgRect);
    CGContextStrokePath(bitmapContext);
    
    
    // 3.获取当前位图上下文的图片
    UIImage *newImage = UIGraphicsGetImageFromCurrentImageContext();
    
    // 4.结束位图编辑
    UIGraphicsEndImageContext();
    
    return newImage;
    
    
}

+ (UIImage *)imageWithColor:(UIColor *)color
{
    // 描述矩形
    CGRect rect = CGRectMake(0.0f, 0.0f, 1.0f, 1.0f);
    
    // 开启位图上下文
    UIGraphicsBeginImageContext(rect.size);
    // 获取位图上下文
    CGContextRef context = UIGraphicsGetCurrentContext();
    // 使用color演示填充上下文
    CGContextSetFillColorWithColor(context, [color CGColor]);
    // 渲染上下文
    CGContextFillRect(context, rect);
    // 从上下文中获取图片
    UIImage *theImage = UIGraphicsGetImageFromCurrentImageContext();
    // 结束上下文
    UIGraphicsEndImageContext();
    
    return theImage;
}


- (void)cornerImageWithSize:(CGSize)size fillColor:(UIColor *)fillColor completion:(void (^)(UIImage *image))completion
{
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        // 耗时操作
        // 开启图形上下文
        UIGraphicsBeginImageContextWithOptions(size, YES, 0);
        CGRect rect = CGRectMake(0, 0, size.width, size.height);
        
        // 设置背景填充颜色
        [fillColor setFill];
        UIRectFill(rect);
        
        // Bezier绘制图形
        UIBezierPath *path = [UIBezierPath bezierPathWithOvalInRect:rect];
        [path addClip];
        [self drawInRect:rect];
        
        // 获得结果
        UIImage *result = UIGraphicsGetImageFromCurrentImageContext();
        
        // 关闭
        UIGraphicsEndImageContext();
        
        // 到主线程中刷新UI, 完成回调
        dispatch_async(dispatch_get_main_queue(), ^{
            
            if (completion != nil) {
                completion(result);
            }
        });
    });
}

@end
```

还有UIimageView+SDWebImage
更多分类使用请查看：https://github.com/yanmingLiu/YMCategory
