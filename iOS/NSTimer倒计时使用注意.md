**创建定时器2中方式**
1. timerWithTimeInterval开头的类方法，需要手动加入RunLoop
2. scheduledTimerWithTimeInterval开头的类方法 这个 NSTimer 以 NSDefaultRunLoopMode 模式放入当前线程的 RunLoop

### 1.timerWithTimeInterval 手动加入RunLoop

1.1 target的方式
```
- (void)targetTimer {
    //mark: target方式创建的timer 需要在viewDidDisappear里面 执行 [self.timer invalidate]; 才会执行dealloc，不然tiemr不会释放
    self.timer = [NSTimer timerWithTimeInterval:1 target:self selector:@selector(countDouwn) userInfo:nil repeats:YES];    
    [[NSRunLoop mainRunLoop] addTimer:self.timer forMode:NSDefaultRunLoopMode];
}   

- (void)viewDidDisappear:(BOOL)animated {
    [super viewDidDisappear:animated];
    
    [self.timer invalidate];
    self.timer = nil;
}

```
1.2 block的方式 ios10以后苹果新API，与 target的方式不同的是，在dealloc方法里面执行invalidate 也行，但是要注意block的循环引用问题
```
- (void)timerBlock {
    __block int i = 1;
    __weak __typeof(&*self)weakSelf = self;
    self.timer = [NSTimer timerWithTimeInterval:1 repeats:YES block:^(NSTimer * _Nonnull timer) {
        i++;
        NSLog(@"%d", i); 
        weakSelf.label.text = [NSString stringWithFormat:@"%d", i];
    }];
    [[NSRunLoop mainRunLoop] addTimer:self.timer forMode:NSDefaultRunLoopMode];
}
```


#### 2.scheduledTimer 自动加入RunLoop

2.1 需要在viewDidDisappear里面 执行 [self.timer invalidate]; 才会执行dealloc
```
/// targetTimer
- (void)scheduledTimer {
    self.timer = [NSTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(countDouwn) userInfo:nil repeats:YES];
}
```

2.2 block的方式 ios10以后苹果新API，与 target的方式不同的是，在dealloc方法里面执行invalidate 也行，但是要注意block的循环引用问题

```
- (void)scheduledTimerBlock {
    __block int i = 1;
    __weak __typeof(&*self)weakSelf = self;
    self.timer = [NSTimer scheduledTimerWithTimeInterval:1 repeats:YES block:^(NSTimer * _Nonnull timer) {
        i++;
        NSLog(@"%d", i); 
        weakSelf.label.text = [NSString stringWithFormat:@"%d", i];
    }];
}
```
