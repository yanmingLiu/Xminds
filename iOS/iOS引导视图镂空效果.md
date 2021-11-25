![999999999.png](https://upload-images.jianshu.io/upload_images/2051176-fda6e3accf95ce12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640)

```
- (void)showViewRect:(CGRect)rect cornerRadius:(CGFloat)cornerRadius
{
    [[UIApplication sharedApplication].keyWindow addSubview:self];
    CGRect frame = [UIScreen mainScreen].bounds;
    [self addSubview:self.imageView];

    // 第一个路径
    UIBezierPath *path = [UIBezierPath bezierPathWithRect:frame];
    // 透明path
    UIBezierPath *path2 = [[UIBezierPath bezierPathWithRoundedRect:rect cornerRadius:cornerRadius] bezierPathByReversingPath];

    [path appendPath:path2];
    // 绘制透明区域
    CAShapeLayer *shapeLayer = [CAShapeLayer layer];
    shapeLayer.path = path.CGPath;
    [self.layer setMask:shapeLayer];

    self.imageView.centerX = CGRectGetCenter(rect).x - self.imageView.width * 0.5;
    self.imageView.y = CGRectGetMaxY(rect) + 20;
}
```

![image.png](https://upload-images.jianshu.io/upload_images/2051176-13113b5127656515.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
@implementation UIView (Guide)

- (void)hollowWithRect:(CGRect)rect cornerRadius:(CGFloat)cornerRadius
{
    CGRect frame = self.bounds;
    // 第一个路径
    UIBezierPath *path = [UIBezierPath bezierPathWithRect:frame];
    // 透明path
    UIBezierPath *path2 = [[UIBezierPath bezierPathWithRoundedRect:rect cornerRadius:cornerRadius] bezierPathByReversingPath];

    [path appendPath:path2];
    // 绘制透明区域
    CAShapeLayer *shapeLayer = [CAShapeLayer layer];
    shapeLayer.path = path.CGPath;
    [self.layer setMask:shapeLayer];
}


- (void)outLineWithlineWidth:(CGFloat)lineWidth
                 strokeColor:(UIColor *)strokeColor
                   fillColor:(UIColor *)fillColor
                cornerRadius:(CGFloat)cornerRadius
{
    CAShapeLayer *border = [CAShapeLayer layer];
    //虚线的颜色
    border.strokeColor = strokeColor.CGColor;
    //填充的颜色
    border.fillColor = fillColor.CGColor;
    
    UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:self.bounds cornerRadius:cornerRadius];
    //设置路径
    border.path = path.CGPath;
    border.frame = self.bounds;
    //虚线的宽度
    border.lineWidth = lineWidth;
    //设置线条的样式
    //    border.lineCap = @"square";
    //虚线的间隔
    border.lineDashPattern = @[@4, @2];
    self.layer.cornerRadius = cornerRadius;
    self.layer.masksToBounds = YES;
    [self.layer addSublayer:border];
}

@end
```

如果是要挖多个区域，上面的设置layer.mask方法就不行，需要用下下面的方法：

```

- (void)hollowWithRect:(CGRect)rect1
                 rect2:(CGRect)rect2
          cornerRadius:(CGFloat)cornerRadius {
    
    [self.layer.sublayers makeObjectsPerformSelector:@selector(removeFromSuperlayer)];
    
    CGRect frame = self.bounds;
    //整体区域
    UIBezierPath *bezierPath = [UIBezierPath bezierPathWithRect:frame];
    //指定区域
    UIBezierPath *clearPath = [UIBezierPath bezierPathWithRoundedRect:rect1 cornerRadius:cornerRadius];
    [bezierPath appendPath: clearPath];
    
    if (!CGRectEqualToRect(rect2, CGRectZero)) {
        UIBezierPath *clearPath2 = [UIBezierPath bezierPathWithRoundedRect:rect2 cornerRadius:cornerRadius];
        [bezierPath appendPath: clearPath2];
    }

    [bezierPath setUsesEvenOddFillRule:YES];

    //创建layer显示
    CAShapeLayer *Layer1 = [CAShapeLayer layer];
    Layer1.path = bezierPath.CGPath;
    [Layer1 setFillRule:kCAFillRuleEvenOdd];
    Layer1.opacity = 0.85;
    //添加到View上
    [self.layer addSublayer:Layer1];
}


```
