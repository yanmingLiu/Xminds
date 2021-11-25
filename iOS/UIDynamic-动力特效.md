>UIDynamic是UIKit动力模型，ios7开始的技术，提供一个模拟真实世界中力学相关点动画和交互系统，比如：重力、碰撞、吸附、摩擦力等。

###使用步骤
#####1.创建物理仿真器   （同时设置物理仿真范围）== 相当于运动场
#####2.创建物理仿真行为（同时设置物理仿真元素）== 行为相当于跑步、元素相当于人
#####3.将行为添加到仿真器中 == 相当于张三在运动场里跑步

------------------------------------------------------------------------------------------
仿真行为有下面几种：
```
UIGravityBehavior       重力行为
UICollisionBehavior     碰撞行为
UIAttachmentBehavior    吸附行为
UIPushBehavior          推力行为
UISnapBehavior          捕捉行为、闪烁行为
UIAttachmentBehavior    附着行为
UIDynamicItemBehavior   摩擦力

```

----------------------------------------------------------

demo：

```
@property (strong, nonatomic) UIView *redView;

@property (strong, nonatomic) UIDynamicAnimator *anim;

// 懒加载anim

- (UIDynamicAnimator *)anim {
    if(!_anim) {
        _anim = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
    }
    return _anim;
}

- (void)viewDidLoad {
    self.redView = [[UIView alloc] initWithFrame:CGRectmake:(10,10,50,50)];
    self.redView.backGroundColor = [UIColor redColor];
    [self.view addSubViews：self.redView];
}

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
   // 1.创建物理仿真器 
   // 2.创建物理仿真行为
      UIGravityBehavior *gravity = [[UIGravityBehavior  alloc] initWithItems:@[self.redView]];
  //  3.将行为添加到仿真器中
      [self.anim addBehavior:gravity ];
}



```

















