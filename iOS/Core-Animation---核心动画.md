>它是一组非常强大的动画处理API。
Core Animation自身并不是一个绘图系统。它只是一个负责在硬件上合成和操纵应用内容的基础构件。
Core Animation的动画执行过程都是在后台操作的，不会阻塞主线程。
Core Animation是直接作用在CALayer上的，并非UIView。 
其实UIView之所以能显示在屏幕上，完全是因为它内部的一个图层。
在创建UIView对象时，UIView内部会自动创建一个图层(即CALayer对象)，通过UIView的layer属性可以访问这个层



###Core Animation类的继承关系图
![CAAnimation继承结构.jpg](http://upload-images.jianshu.io/upload_images/2051176-f2c436fcab169204.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在Core Animation中，动画抽象出来一个父类CAAnimation，一般不直接使用这个父类实现动画，而是使用它的子类来完成动画的设置
					常用的两个子类动画为 CAKeyframeAnimation和CABasicAnimation

###使用步骤:
1.初始化一个动画对象(CAAnimation)并设置一些动画相关属性 
```
CABasicAnimation *anima = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
anima.toValue = [NSNumber numberWithFloat:2.0f];
anima.duration = 1.0f;
```
2.添加动画对象到层(CALayer)中，开始执行动画
```
[_imgView.layer addAnimation:anima forKey:@nil];
```

>####animationWithKeyPath的值：
 transform.scale = 比例缩放
 transform.scale.x 
transform.scale.y 
transform.rotation.z   旋转 
opacity                       透明度
margin
zPosition
backgroundColor    背景颜色
cornerRadius    圆角
borderWidth
bounds
contents
contentsRect
cornerRadius
frame
hidden
mask
masksToBounds
opacity
position
shadowColor
shadowOffset
shadowOpacity
shadowRadius

####一、**CABaseAnimation**  **基础动画**

>重要属性
![属性.png](http://upload-images.jianshu.io/upload_images/2051176-c9dd86a2f1ef2914.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
//使用CABasicAnimation创建基础动画
CABasicAnimation *anima = [CABasicAnimation animationWithKeyPath:@"position"];
anima.fromValue = [NSValue valueWithCGPoint:CGPointMake(10, 10)];
anima.toValue = [NSValue valueWithCGPoint:CGPointMake(200,200)];
anima.duration = 1.0f;

// 注意点
// 如果fillMode=kCAFillModeForwards和removedOnComletion=NO，那么在动画执行完毕后，图层会保持显示动画执行后的状态。但在实质上，图层的属性值还是动画执行前的初始值，并没有真正被改变。
//anima.fillMode = kCAFillModeForwards; //保存当前状态
//anima.removedOnCompletion = NO; //动画对象不要移除

[_imgView.layer addAnimation:anima forKey:nil];
```

####二、**CAKeyframeAnimation****关键帧动画**
CAKeyframeAnimation和CABaseAnimation都属于CAPropertyAnimatin的子类。CABaseAnimation只能从一个数值（fromValue）变换成另一个数值（toValue）,而CAKeyframeAnimation则会使用一个NSArray保存一组关键帧。

>重要属性
values：就是上述的NSArray对象。里面的元素称为”关键帧”(keyframe)。动画对象会在指定的时间(duration)内，依次显示values数组中的每一个关键帧
path：可以设置一个CGPathRef\CGMutablePathRef,让层跟着路径移动。path只对CALayer的anchorPoint和position起作用。如果你设置了path，那么values将被忽略。
keyTimes：可以为对应的关键帧指定对应的时间点,其取值范围为0到1.0,keyTimes中的每一个时间值都对应values中的每一帧.当keyTimes没有设置的时候,各个关键帧的时间是平分的。


```
/**
 *  抖动效果
 */
-(void)shakeAnimation{
    CAKeyframeAnimation *anima = [CAKeyframeAnimation animationWithKeyPath:@"transform.rotation"];//在这里@"transform.rotation"==@"transform.rotation.z"
    NSValue *value1 = [NSNumber numberWithFloat:-M_PI/180*4];
    NSValue *value2 = [NSNumber numberWithFloat:M_PI/180*4];
    NSValue *value3 = [NSNumber numberWithFloat:-M_PI/180*4];
    anima.values = @[value1,value2,value3];
    anima.repeatCount = MAXFLOAT;
    
    [_demoView.layer addAnimation:anima forKey:@"shakeAnimation"];

}

```


####三、**CAAnimationGroup****组动画**

>重要属性
animations: 用来保存一组动画对象的NSArray

```
CAKeyframeAnimation *anima1 = [CAKeyframeAnimation animationWithKeyPath:@"position"];
NSValue *value0 = [NSValue valueWithCGPoint:CGPointMake(0, SCREEN_HEIGHT/2-50)];
NSValue *value1 = [NSValue valueWithCGPoint:CGPointMake(SCREEN_WIDTH/3, SCREEN_HEIGHT/2-50)];
NSValue *value2 = [NSValue valueWithCGPoint:CGPointMake(SCREEN_WIDTH/3, SCREEN_HEIGHT/2+50)];
NSValue *value3 = [NSValue valueWithCGPoint:CGPointMake(SCREEN_WIDTH*2/3, SCREEN_HEIGHT/2+50)];
NSValue *value4 = [NSValue valueWithCGPoint:CGPointMake(SCREEN_WIDTH*2/3, SCREEN_HEIGHT/2-50)];
NSValue *value5 = [NSValue valueWithCGPoint:CGPointMake(SCREEN_WIDTH, SCREEN_HEIGHT/2-50)];
anima1.values = [NSArray arrayWithObjects:value0,value1,value2,value3,value4,value5, nil];
//缩放动画
CABasicAnimation *anima2 = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
anima2.fromValue = [NSNumber numberWithFloat:0.8f];
anima2.toValue = [NSNumber numberWithFloat:2.0f];
//旋转动画
CABasicAnimation *anima3 = [CABasicAnimation animationWithKeyPath:@"transform.rotation"];
anima3.toValue = [NSNumber numberWithFloat:M_PI*4];
//组动画
CAAnimationGroup *groupAnimation = [CAAnimationGroup animation];
groupAnimation.animations = [NSArray arrayWithObjects:anima1,anima2,anima3, nil];
groupAnimation.duration = 3.0f;
[_imgView.layer addAnimation:groupAnimation forKey:@"groupAnimation"];
```

####四、**CATransition****过渡动画**
CAAnimation的子类，用于做过渡动画或者转场动画，能够为层提供移出屏幕和移入屏幕的动画效果。

>重要属性
* type：动画过渡类型
* subtype：动画过渡方向
* startProgress：动画起点(在整体动画的百分比)
* endProgress：动画终点(在整体动画的百分比)
**注意点**
私有api，不建议开发者们使用。因为苹果公司不提供维护，并且有可能造成你的app审核不通过。

**type:动画类型(比如：滴水效果，翻转效果...)**
fade kCATransitionFade  -------- 交叉淡化过渡
 moveIn kCATransitionMoveIn --- 新视图移到旧视图上面
 push kCATransitionPush  ------- 新视图把旧视图推出去
 reveal kCATransitionReveal----- 将旧视图移开,显示下面的新视图
 pageCurl    -----------------------  向上翻一页
 pageUnCurl  -------------------- 向下翻一页
 rippleEffect ---------------------滴水效果
 suckEffect --------------------- 收缩效果，如一块布被抽走
 cube----------------------------立方体效果
 oglFlip -------------------------上下左右翻转效果
 rotate---------------------------旋转效果
 cameraIrisHollowClose---------- 相机镜头关上效果(不支持过渡方向)
 cameraIrisHollowOpen ----------相机镜头打开效果(不支持过渡方向)
 
 *******************************************************
** subtype: 动画方向(比如说是从左边进入，还是从右边进入...)**

 kCATransitionFromRight;
 kCATransitionFromLeft;
 kCATransitionFromTop;
 kCATransitionFromBottom;
 
**当 type 为@"rotate"(旋转)的时候,它也有几个对应的 subtype,分别为:**
 90cw 逆时针旋转 90°
 90ccw 顺时针旋转 90°
 180cw 逆时针旋转 180°
 180ccw  顺时针旋转 180°

```
// 翻转效果
-(void)oglFlipAnimation{
    CATransition *anima = [CATransition animation];
    anima.type = @"oglFlip";//设置动画的类型
    anima.subtype = kCATransitionFromRight; //设置动画的方向
    anima.duration = 1.0f;
    
    [_demoView.layer addAnimation:anima forKey:nil];
}
```

####五. **UIView动画****仿射变换**

>+ (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^)(BOOL finished))completion
参数解析:
duration：动画的持续时间
delay：动画延迟delay秒后开始
options：动画的节奏控制
animations：将改变视图属性的代码放在这个block中
completion：动画结束后，会自动调用这个block

```
-(void)scaleAnimation{
    _imgView.transform = CGAffineTransformIdentity;
    [UIView animateWithDuration:1.0 animations:^{
        _imgView.transform = CGAffineTransformMakeScale(2, 2);
    } completion:^(BOOL finished) {
        _imgView.transform = CGAffineTransformIdentity;
    }];
}
```

-------------------------------------------

很好很强大的demo：
https://github.com/yixiangboy/IOSAnimationDemo
