1. 什么是函数式编程

Functional Programming的定义：
```
In computer science, functional programming is a programming paradigm that treats computation as the evaluation of mathematical functions and avoids state and mutable data.
```
简单地翻译一下，也就是说函数式编程是一种编程模型，他将计算机运算看做是数学中函数的计算，并且避免了状态以及变量的概念。

2.作为一个暴躁的🐒，拜托直接上硬菜，简单、直接的、别墨迹。

3.世界上最遥远的距离，明明我在你身边你却不知道我爱你。Masonry 就是我们最常见的函数式编程,通过对象.方法1().方法2.....
```
make.edges.equalTo(sv).with.insets(UIEdgeInsetsMake(5, 5, 5, 5));
```

4.了解一个人最快的方式，没错 你懂的 —— 感同身受，实践是检验真理的唯一标准：韩国美女多吗？ 中国更多好吗。

###.h
```
#import <Foundation/Foundation.h>

@interface Person : NSObject

/**
 * 1.初级写法，一般我们给类添加的方法
 */
- (void)run1;
- (void)eat1;


/**
 * 2.链式编程 调用方法编程, 
 *   调用方式为 [[p run2] ert2]
 *   可以发现还是用对象去调用方法，那么就需要一个对象的返回值 而不是void
 */
- (Person *)run2;
- (Person *)eat2;

/**
 * 3.函数式编程
 *   调用方式为 p.run3().eat3()
 *   可以发现这种方式为对象直接链式调用p.run3().eat3()
 *   OC中在执行block的时候需要'()'
 *
 */

// 不带参数
- (Person * (^)())run3;
- (Person * (^)())eat3;

// 带参数的block
- (Person * (^)(NSString *str))run4;
- (Person * (^)(NSString *str))eat4;

@end
```
###.m

```
#import "Person.h"

@implementation Person


/**
 * 1.初级写法，一般我们给类添加的方法
 */
- (void)run1 {
    NSLog(@"------run");
}

- (void)eat1 {
    NSLog(@"------eat");
}

/**
 * 2.链式编程
 */
- (Person *)run2 {
    NSLog(@"------run2");
    return self;
}

- (Person *)eat2 {
    NSLog(@"------eat2");
    return self;
}


/**
 * 2.函数式编程
 */
- (Person *(^)())run3 {
    return ^() {
        NSLog(@"------run3");
        return self;
    };
}

- (Person *(^)())eat3 {
    return ^() {
        NSLog(@"------eat3");
        return self;
    };
}

- (Person *(^)(NSString *))run4 {
    return ^(NSString *str) {
        NSLog(@"run4--%@", str);
        return self;
    };
}

- (Person *(^)(NSString *))eat4 {
    return ^(NSString *str) {
        NSLog(@"eat4--%@", str);
        return self;
    };
}

@end
```

###调用
```
- (void)viewDidLoad {
    [super viewDidLoad];
    
    Person *p = [[Person alloc] init];
    
    // 1.第一种写法调用函数，使用对象分别调用方法
    [p run1];
    [p eat1];
    
    // 2.链式编程
    [[p run2] eat2];
    
    // 3.函数式编程
    p.run3().eat3();
    
    p.run4(@"皇后大道东").eat4(@"吃包子");
}
```

###demo地址：https://github.com/yanmingLiu/FuncProgram
