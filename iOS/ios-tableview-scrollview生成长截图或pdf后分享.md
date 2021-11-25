iOS 13上问题，这个博客已经处理：[iOS 13 ScrollView截图问题记录](https://freelf.me/iOS-13-ScrollView%E6%88%AA%E5%9B%BE%E9%97%AE%E9%A2%98%E8%AE%B0%E5%BD%95)；

我们需求是生成pdf，然后分享，刚好yykit里面view有生成pdf的方法，正好这个方式也是将scrollview放到临时view里面来生成的图片，正好合适。

###yykit生成pdf方法
```
- (NSData *)snapshotPDF {
    CGRect bounds = self.bounds;
    NSMutableData *data = [NSMutableData data];
    CGDataConsumerRef consumer = CGDataConsumerCreateWithCFData((__bridge CFMutableDataRef)data);
    CGContextRef context = CGPDFContextCreate(consumer, &bounds, NULL);
    CGDataConsumerRelease(consumer);
    if (!context) return nil;
    CGPDFContextBeginPage(context, NULL);
    CGContextTranslateCTM(context, 0, bounds.size.height);
    CGContextScaleCTM(context, 1.0, -1.0);
    [self.layer renderInContext:context];
    CGPDFContextEndPage(context);
    CGPDFContextClose(context);
    CGContextRelease(context);
    return data;
}
```

###获取scrollView的长截图

```

/** 获取tableview的长截图*/
- (NSData *)snapshotScrollViewPDF:(UITableView *)tableview {

    UITableView *scrollView = tableview;
    
    // 保存原来的偏移量
    CGPoint savedContentOffset = scrollView.contentOffset;
    CGRect savedFrame = scrollView.frame;
    
    // 设置截图需要的偏移量和frame
    scrollView.contentOffset = CGPointZero;
    scrollView.frame = CGRectMake(0, 0, scrollView.contentSize.width, scrollView.contentSize.height);
    
    // 创建临时view，并且把要截图的view添加到临时view上面
    UIView *tempView = [[UIView alloc]initWithFrame:CGRectMake(0, 0, scrollView.contentSize.width, scrollView.contentSize.height)];
    [scrollView removeFromSuperview];
    [tempView addSubview:scrollView];
    
    // 对临时view进行截图
    [tempView.layer renderInContext:UIGraphicsGetCurrentContext()];
    
    // yykit方法
    NSData *data = [tempView snapshotPDF];
    
    // 恢复截图view原来的状态
    [scrollView removeFromSuperview];
    [self.view addSubview:scrollView];
    scrollView.contentOffset = savedContentOffset;
    
    // 如果原来是frame布局，需要设置frame，如果是Auto layout需要再次进行Auto layout布局。
    scrollView.frame = savedFrame;
    //        [scrollView mas_makeConstraints:^(MASConstraintMaker *make)     {
    //            make.edges.equalTo(self);
    //        }];
    return data;
}
```

###保存pdf 并分享
```

/// 导出pdf并 分享
- (void)exportPDF {
    NSData *pdfData = [self snapshotScrollViewPDF:self.contentTableView];
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        //  二进制流写入文件
        NSString *documentsDirectory = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES)lastObject];
        NSFileManager *fileManger = [NSFileManager defaultManager];
        NSString *testDirectory = [documentsDirectory stringByAppendingString:@"/pdf"];
        //  创建目录
        [fileManger createDirectoryAtPath:testDirectory withIntermediateDirectories:YES attributes:nil error:nil];
        //  创建文件
        NSString *testPath = [testDirectory stringByAppendingPathComponent:@"签约协议.pdf"];
        //  写入文件
        [fileManger createFileAtPath:testPath contents:pdfData attributes:nil];
        
        dispatch_sync(dispatch_get_main_queue(), ^{
            NSURL *pdfURL = [NSURL fileURLWithPath:testPath];
            UIActivityViewController *ac = [[UIActivityViewController alloc] initWithActivityItems:@[pdfData, pdfURL] applicationActivities:nil];
            [self presentViewController:ac animated:YES completion:nil];
        });
    });
}
```
