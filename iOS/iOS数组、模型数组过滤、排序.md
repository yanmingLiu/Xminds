
### 利用谓词根据一定条件从一个数组中过滤数据
>  谓词关键字:
     1.比较: >,<,==,>=,<=,!=
     2.范围: IN、BETWEEN
     3.字符串相关：CONTAINS(包含)、 BEGINSWITH(以某个字符串开头)、ENDSWITH(以某个字符串结束)
        NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"name CONTAINS[cd] %@",text];
     4.通配符: LIKE
        NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"name LIKE[cd] '*er'"];
     5.正则匹配: MATCHES
        NSPredicate *filterPredicate = [NSPredicate predicateWithFormat:@"name MATCHES 'Z.+e$'"];
     6.组合查询 AND

### 普通数组过滤
```
 NSArray * arr1 = @[@2,@3,@4,@5];
 NSArray * arr2 = @[@4,@3,@2,@1];
 
 NSPredicate * predicate = [NSPredicate predicateWithFormat:@"SELF > 4",arr1];
 NSArray *result = [arr1 filteredArrayUsingPredicate:predicate];
 NSLog(@"arr1中大于4的数据:%@", result);
 
 NSPredicate * predicate2 = [NSPredicate predicateWithFormat:@"SELF BETWEEN {3, 5}",arr1];
 NSArray *result2 = [arr1 filteredArrayUsingPredicate:predicate2];
 NSLog(@"arr1中3-5的数据:%@", result2);
 
 NSPredicate * filterPredicate1 = [NSPredicate predicateWithFormat:@"NOT (SELF IN %@)",arr1];
 NSArray * filter1 = [arr2 filteredArrayUsingPredicate:filterPredicate1];
 NSLog(@"只在arr2中的数据:%@", filter1);

 NSPredicate * filterPredicate2 = [NSPredicate predicateWithFormat:@"NOT (SELF IN %@)",arr2];
 NSArray * filter2 = [arr1 filteredArrayUsingPredicate:filterPredicate2];
 NSLog(@"只在arr1中的数据:%@",filter2);

 NSMutableArray *array = [NSMutableArray arrayWithArray:filter1];
 [array addObjectsFromArray:filter2];
 NSLog(@"arr1、arr2中不同的数据:%@",array);


NSPredicate * filterPredicate_same = [NSPredicate predicateWithFormat:@"SELF IN %@",arr1];
NSArray * filter_no = [arr2 filteredArrayUsingPredicate:filterPredicate_same];
NSLog(@"arr1、arr2中相同的数据:%@", filter_no);

```



####   模型数组排序
```
NSMutableArray *mArr = self.dataArray;
[mArr sortUsingComparator:^NSComparisonResult(Model * obj1, Model * obj2) {
    return [@(obj1.total.doubleValue) compare:@(obj2.total.doubleValue)];
}];
```

### 倒叙
```
NSMutableArray *tempArr = [NSMutableArray arrayWithObjects:@"5",@"1",@"4",@"2",nil];
    tempArr = (NSMutableArray *)[[tempArr reverseObjectEnumerator] allObjects];
    NSLog(@"%@", tempArr);
// 2 4 1 5
```
### makeObjectsPerformSelector
>  1.makeObjectsPerformSelector:@select（aMethod）
     简介：让数组中的每个元素 都调用 aMethod

```
 [array makeObjectsPerformSelector:@selector(doSomeThing)]; 
```
>  2.makeObjectsPerformSelector:@select（aMethod） withObject:oneObject
    简介：让数组中的每个元素 都调用 aMethod  并把 withObject 后边的 oneObject 对象做为参数传给方法aMethod
    
    [array makeObjectsPerformSelector:@selector(setRecurringDelegate:) withObject:self];


  > 常见用法使用场景 避免cell重用


```
[cell.contentView.subviews makeObjectsPerformSelector:@selector(removeFromSuperview)];
```

### enumerateObjectsUsingBlock
>通常enumerateObjectsUsingBlock: 和 (for(... in ...)在效率上基本一致，有时会快些。主要是因为它们都是基于 NSFastEnumeration 实现的. 快速迭代在处理的过程中需要多一次转换，当然也会消耗掉一些时间. 基于Block的迭代可以达到本机存储一样快的遍历集合. 对于字典同样适用，而数组的迭代却不行。

遍历数组
```

    NSArray *arr = @[
                     @"http://www.baidu.com/1483496523118.jpg",
                     @"https://www.baidu.com/1483499173873.jpg",
                     @"<null>",
                     @"https://www.baidu.com/1483612409665.jpg",
                     @"<null>"
                     ];
    NSMutableArray *arr1 = [NSMutableArray arrayWithArray:arr];
    
    [arr1 enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        if ([obj isKindOfClass:[NSNull class]] || [obj isEqualToString:@"<null>"] || [obj isEqualToString:@""]) {
            [arr1 removeObject:obj];
        }
    }];
    NSLog(@"%@", arr1);
```
