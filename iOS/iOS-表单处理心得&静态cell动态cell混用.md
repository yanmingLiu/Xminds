
>前言：在大量的表单处理中过程中总是会面临这些问题，面对多种cell，行数众多，操作繁琐、显示隐藏等等的问题。


######一：UI设计图

![Group.png](https://upload-images.jianshu.io/upload_images/2051176-105dd495ed6cf558.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



######二、面对这样的UI设计，处理方式大致有一下集中：
1.纯代码撸各种样式cell。（工作量大、cell种类多，pass）
2.xib storyboar托scrollView，把一个个cell单独用View封起来布局。(显示隐藏还需要修改约束，操作繁琐，pass)
4.通常UI会在表单最下面放悬浮的保存按钮，而不是放导航栏，对于这种有2种处理方式：
4.1 storyboar上托viewController + containerView，这种方式视图层级多了一层，处理数据需要控制2个控制器，不推荐
![QQ20190129-141751@2x.png](https://upload-images.jianshu.io/upload_images/2051176-4db24576e69d96c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


4.2 面对“保存”按钮放在导航栏的时候，直接用storyboar托静态tableView就好
4.3 面对“保存”按钮在最下方的情况，storyboar上托静态tableView ，用代码添加悬浮在底部的保存按钮。
**注意1：这种方式直接把保存按钮的view放到tableView上是不行的，他会随着tableView滚动，就算监听scrollView滚动动态改变按钮view的y值也有问题，底部的view会有穿透的效果。**

![DF2B56C2-324A-4CBA-BE65-DE26717D7552.png](https://upload-images.jianshu.io/upload_images/2051176-161c8418a16b1b2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**注意2：静态cell动态cell混用，1>动态section必须要保留1个cell, 2>cell的缩进级别代理方法，动态静态cell必须重写，否则会造成崩溃**

```
// MARK: - tableDataSource

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{
    if(section == SectionTypeSKU){// （动态cell）
        return self.pinModel.skuList.count;
    }
    if (section == SectionTypeLimit && !self.switchBtn.on) {
        return 0;
    }
    return [super tableView:tableView numberOfRowsInSection:section];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
    if(SectionTypeSKU == indexPath.section){//（动态cell）
        SkuInputCell *cell = [SkuInputCell cellWithTableView:tableView];
        return cell;
    }
    return [super tableView:tableView cellForRowAtIndexPath:indexPath];
}

// MARK: - tableviewDelegate

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath{
    if(SectionTypeSKU == indexPath.section){//（动态cell）
        return 154;
    }
    return [super tableView:tableView heightForRowAtIndexPath:indexPath];
}

//cell的缩进级别，动态静态cell必须重写，否则会造成崩溃
- (NSInteger)tableView:(UITableView *)tableView indentationLevelForRowAtIndexPath:(NSIndexPath *)indexPath{
    if(SectionTypeSKU == indexPath.section){// （动态cell）
        return [super tableView:tableView indentationLevelForRowAtIndexPath: [NSIndexPath indexPathForRow:0 inSection:indexPath.section]];
    }
    return [super tableView:tableView indentationLevelForRowAtIndexPath:indexPath];
}
```

**注意3：代码添加底部保存view直接添加到self.navigationController.view上，但是视图消失的时候一定要移除**

```

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];

    [self.navigationController.view addSubview:self.bottomView];
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];

    [self.bottomView removeFromSuperview];
}
```


效果图：

![QQ20190129-143957@2x.png](https://upload-images.jianshu.io/upload_images/2051176-8cbce0d4e3b191e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





