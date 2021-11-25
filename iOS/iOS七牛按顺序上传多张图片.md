####上传图片步骤：
1.配置QNUploadManager
```
+ (QNUploadManager *)uploadManager {
    QNConfiguration *config = [QNConfiguration build:^(QNConfigurationBuilder *builder) {
        NSMutableArray *array = [[NSMutableArray alloc] init];
        [array addObject:[QNResolver systemResolver]];
        QNDnsManager *dns = [[QNDnsManager alloc] init:array networkInfo:[QNNetworkInfo normal]];
        builder.dns = dns;
        //是否选择  https  上传
        builder.useHttps = YES;
        builder.zone = [[QNAutoZone alloc] initWithDns:dns];
        //设置断点续传
        NSError *error;
        builder.recorder =  [QNFileRecorder fileRecorderWithFolder:@"保存目录" error:&error];
    }];
    QNUploadManager* upManager = [[QNUploadManager alloc] initWithConfiguration:config];

    return upManager;
}

+ (QNUploadOption *)uploadOption {
    QNUploadOption* uploadOption = [[QNUploadOption alloc] initWithMime:nil progressHandler:^(NSString *key, float percent) {
        //        NSLog(@"percent == %.2f", percent);
    } params:nil checkCrc:NO cancellationSignal:nil];
    return uploadOption;
}
```

2.从服务器获取上传公用或私有的token和domain
3. 按顺序一张张上传图片

```

/// 上传图片方式
typedef NS_ENUM(NSUInteger, QNUpdateImageType) {
    QNUpdateImageTypePublic,   // 公有
    QNUpdateImageTypePrivate,  // 私有
};

/// 上传图片回调
typedef void(^UploadImageCompleted)(BOOL success, NSString* msg, NSArray* keys);

/// 顺序上传图片
+ (void)uploadImages:(NSArray *)imageArray updateType:(QNUpdateImageType)updateType callback:(UploadImageCompleted)callback {

    if (imageArray.count == 0) {
        callback(NO, @"没有图片", nil);
        return;
    }

    /// 这里是获取七牛上传的token（演示代码）：可以单独获取然后存储到本地 不用每次都请求
    [self getQiNiuToken:updateType callback:^(BOOL success, NSString *token, NSString *domain) {
        if (!success) {
            callback(NO, @"获取token失败", nil);
        }
        else {
            [self uploadImages:imageArray index:0 token:token domain:domain keys:[NSMutableArray array] completed:callback];
        }
    }];
}


/// 递归按图片顺序上传
+ (void)uploadImages:(NSArray *)imageArray index:(NSInteger)index token:(NSString *)token domain:(NSString *)domain keys:(NSMutableArray *)keys completed:(UploadImageCompleted)completed
{
    __block NSMutableArray *images = [NSMutableArray arrayWithArray:imageArray];
    __block NSUInteger currentIndex = index;
    __block UIImage *image = images[currentIndex];

    if (image == nil || completed == nil){
        completed(NO, @"无效图片", nil);
        return;
    }
    NSData* data = UIImageJPEGRepresentation(image, 1);
    //NSInteger size = [data length];

    [[self uploadManager] putData:data key:nil token:token complete:^(QNResponseInfo *info, NSString *key, NSDictionary *resp) {
        if (info.isOK) {
            currentIndex++;
            key = [resp objectForKey:@"key"];
            
            // 拼接url，有的后台处理只需要图片key 直接返回keys就行
            NSString *url = [domain stringByAppendingString:key];
            NSLog(@"url===%@", url);
            [keys addObject:url];

            if ([keys count] == [imageArray count]) {
                completed(YES, @"全部上传完成", keys);
                return;
            }else {
                [self uploadImages:images index:currentIndex token:token domain:domain keys:keys completed:completed];
            }
        }
        else {
            completed(NO, @"上传图片失败,稍后重试", nil);
        }
    }  option:[self uploadOption]];
}

```

