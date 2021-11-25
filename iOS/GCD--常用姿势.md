
#### for循环+信号量
```

- (void)gcd_for {
    NSLog(@"异步串行操作");
    // 1.创建异步串行队列，保证for循环依次执行
    dispatch_queue_t serialQueue = dispatch_queue_create("aaa", DISPATCH_QUEUE_SERIAL);
    // 2.创建信号量0
    dispatch_semaphore_t sem = dispatch_semaphore_create(0);

    dispatch_async(serialQueue, ^{

        __block NSMutableArray *tampArray = [NSMutableArray array];

        for (int i = 0; i<5; i++) {
            // 3.异步网络请求
            NSMutableURLRequest *urlRequest = [[NSMutableURLRequest alloc] initWithURL:[NSURL URLWithString:@"https://github.com"]];
            NSURLSession *session = [NSURLSession sharedSession];
            NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:urlRequest completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

                [tampArray addObject:@(i)];
                NSLog(@"任务%d:%@",i,[NSThread currentThread]);
                // 3.信号量+1
                dispatch_semaphore_signal(sem);
            }];
            [dataTask resume];

            // 4.//等待信号量
            dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);
        }

        // 队列执行完成
        NSLog(@"其他操作 -- %@", [NSThread currentThread]);
        for (NSNumber *num in tampArray) {
            NSLog(@"所有操作完成后的操作--->   %@\n",num);
        }

    });
}

```

####1.同步 主线程 串行 死锁
```
// MARK: 同步 主线程 串行 死锁
- (void)syncMain {
    NSLog(@"0");
    dispatch_sync(dispatch_get_main_queue(), ^{
        NSLog(@"1 %@",[NSThread currentThread]);
    });
    NSLog(@"2 %@",[NSThread currentThread]);
    // 0
}

```

####2.异步 主线程
```
// MARK: 1.异步 主线程(一般用做刷新UI操作) FIFO:(先进先出)
- (void)asyncMain {
    dispatch_queue_t mainQueue = dispatch_get_main_queue();
    dispatch_async(mainQueue, ^{
        NSLog(@"1 %@",[NSThread currentThread]);
    });
    dispatch_async(mainQueue, ^{
        NSLog(@"2 %@",[NSThread currentThread]);
    });
    dispatch_async(mainQueue, ^{
        NSLog(@"3 %@",[NSThread currentThread]);
    });
    // 1 2 3
}
```

####3.线程通信
```
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{  
    // 耗时的操作  
    dispatch_async(dispatch_get_main_queue(), ^{  
        // 更新界面  
    });  
});
```


####4.延迟执行
```
// GCD延迟执行
double delayTime = 2.0;
dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, delayTime * NSEC_PER_SEC);
dispatch_after(popTime, dispatch_get_main_queue(), ^{
    
});

// performSelector 延迟执行
[self performSelector:@selector(doSomeThing) withObject:self afterDelay:2];

// NSTimer 延迟执行 repeats:是否重复,设置为yes = 每2秒执行一次test
[NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(test) userInfo:nil repeats:NO];

```

####5.组
```
// MARK: group_async
- (void)group_async {
    dispatch_group_t group = dispatch_group_create(); 
    dispatch_queue_t globalQueue = dispatch_get_global_queue(0, 0); 

    dispatch_group_async(group, globalQueue, ^{
        NSLog(@"1  %@",[NSThread currentThread]);
    });
    dispatch_group_async(group, globalQueue, ^{
        NSLog(@"2  %@",[NSThread currentThread]);
    });
    dispatch_group_async(group, globalQueue, ^{
        NSLog(@"3  %@",[NSThread currentThread]);
    });
    
    dispatch_group_notify(group, globalQueue, ^{

    });
    // 1 2 3 无序
}

```
####6. 栅栏方法 dispatch_barrier_async
有时需要异步执行两组操作，而且第一组操作执行完之后才能开始执行第二组操作。如果没有dispatch_barrier_async这个方法，这4个任务执行是没有先后顺序的，完全随机，那么加上这个栅栏方法之后，会严格按照先随机顺序执行完栅栏之前的所有任务，然后再执行栅栏方法之后的任务。

dispatch_barrier_async 函数只有在 DISPATCH_QUEUE_CONCURRENT 队列中才起作用,
在全局并发队列 dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0) 中无效

```
// MARK: 栅栏方法 dispatch_barrier_async
- (void)dispatch_barrier_async {
    // 这里不能使用 dispatch_get_global_queue
    dispatch_queue_t queue = dispatch_queue_create("abc", DISPATCH_QUEUE_CONCURRENT);
    
    dispatch_async(queue, ^{
        NSLog(@"1  %@", [NSThread currentThread]);
    });
    dispatch_async(queue, ^{
        NSLog(@"2  %@", [NSThread currentThread]);
    });
    
    // 子线程
    dispatch_barrier_async(queue, ^{ 
        NSLog(@"/ %@", [NSThread currentThread]);
    });
    
    // 主线程
//    dispatch_barrier_sync(queue, ^{
//        NSLog(@"// %@", [NSThread currentThread]);
//    });
    
    dispatch_async(queue, ^{
        NSLog(@"3  %@", [NSThread currentThread]);
    });
    dispatch_async(queue, ^{
        NSLog(@"4  %@", [NSThread currentThread]);
    });
    //  1 2 / 3 4
}
```

#### 7.信号量

```
// 1.创建信号
    dispatch_semaphore_t sema = dispatch_semaphore_create(0);

    // 2.异步执行
    dispatch_async(dispatch_get_global_queue(0, 0), ^{

        // 4.unlock 通知异步调用结果
        dispatch_semaphore_signal(sema);
    });

    // 3.lock 等待异步结果
    dispatch_semaphore_wait(sema, DISPATCH_TIME_FOREVER);

    // 5.处理结果
```
