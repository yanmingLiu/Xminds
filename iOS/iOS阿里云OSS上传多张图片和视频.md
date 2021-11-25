1.从服务器获取上传需要的 accessKeyId， accessKeySecret， securityToken；如果你们是本地直接配置，不需要从服务器拿直接省略。

2.配置OSSTask

3.1  上传图片
```
/// 上传回调
typedef void(^uploadCallblock)(BOOL success, NSString* msg, NSArray<NSString *>* keys);

/// 上传图片 
+ (void)uploadImages:(NSArray *)images isAsync:(BOOL)isAsync callback:(uploadCallblock)callback {
    // 1
    [self getOSSAuth:^(OSSAuthModel *OSSAuth) {
        if (!OSSAuth) {
            if (callback) callback(NO ,@"获取上传token失败", nil);
            return ;
        }

        // 2
        id<OSSCredentialProvider> credential = [[OSSStsTokenCredentialProvider alloc] initWithAccessKeyId:OSSAuth.accessKeyId secretKeyId:OSSAuth.accessKeySecret securityToken:OSSAuth.securityToken];

        OSSClient *client = [[OSSClient alloc] initWithEndpoint:kOSSEndpoint credentialProvider:credential];

        NSOperationQueue *queue = [[NSOperationQueue alloc] init];
        queue.maxConcurrentOperationCount = images.count;

        NSMutableArray *callBackNames = [NSMutableArray array];
        int i = 0;
        for (UIImage *image in images) {
            if (image) {
                NSBlockOperation *operation = [NSBlockOperation blockOperationWithBlock:^{
                    //任务执行
                    OSSPutObjectRequest * put = [OSSPutObjectRequest new];
                    put.bucketName = kOSSBucketName;

                    NSString *imageName = [NSString stringWithFormat:@"%@%@", OSSAuth.folder,[NSString randomStringWithLength:8]];
                    NSString *suffix = [NSString stringWithFormat:@"-%zdx%zd.png", (NSInteger)image.size.width,(NSInteger)image.size.height];
                    imageName = [imageName stringByAppendingString:suffix];

                    put.objectKey = imageName;

                    // 传出url
                    NSString *imageUrl = [NSString stringWithFormat:@"%@/%@", OSSAuth.domain,imageName];

                    [callBackNames addObject:imageUrl];

                    NSData *data = UIImageJPEGRepresentation(image, 0.5);
                    put.uploadingData = data;

                    OSSTask * putTask = [client putObject:put];
                    [putTask waitUntilFinished]; // 阻塞直到上传完成

                    if (!putTask.error) {
                        NSLog(@"upload object success! \nimageUrl:%@\n", imageUrl);
                    } else {
                        NSLog(@"upload object failed, error: %@" , putTask.error);
                    }
                    if (isAsync) {
                        if (image == images.lastObject) {
                            NSLog(@"upload object finished!");
                            if (callback) {
                                callback( YES, @"全部上传完成" , [callBackNames copy]);
                            }
                        }
                    }
                }];
                if (queue.operations.count != 0) {
                    [operation addDependency:queue.operations.lastObject];
                }
                [queue addOperation:operation];
            }
            i++;
        }
        if (!isAsync) {
            [queue waitUntilAllOperationsAreFinished];

            if (callback) {
                callback( YES, @"全部上传完成" , [callBackNames copy]);
            }
        }
    }];
}


```

3.2 上传视频

```
/// 上传视频

+ (void)asyncUploadVideo:(NSData *)data callback:(uploadCallblock)callback {
  
    // 1
    [self getOSSAuth:^(OSSAuthModel *OSSAuth) {
        if (!OSSAuth) {
            if (callback) callback(NO ,@"获取上传token失败" , nil);
            return ;
        }
        if (data.length > 52428800) {
            if (callback) callback(NO ,@"视频文件最大不能超过50M" , nil);
            return;
        }
        
        // 2
        id<OSSCredentialProvider> credential = [[OSSStsTokenCredentialProvider alloc] initWithAccessKeyId:OSSAuth.accessKeyId secretKeyId:OSSAuth.accessKeySecret securityToken:OSSAuth.securityToken];

        OSSClient *client = [[OSSClient alloc] initWithEndpoint:kOSSEndpoint credentialProvider:credential];

        //任务执行
        OSSPutObjectRequest * put = [OSSPutObjectRequest new];
        put.bucketName = kOSSBucketName;

        NSString *imageName = [NSString stringWithFormat:@"%@%@.mp4", OSSAuth.folder,[NSString randomStringWithLength:8]];
        put.objectKey = imageName;

        // 3
        // 传出url
        NSString *imageUrl = [NSString stringWithFormat:@"%@/%@", OSSAuth.domain,imageName];

        put.uploadingData = data;

        OSSTask * putTask = [client putObject:put];
        [putTask waitUntilFinished]; // 阻塞直到上传完成
        if (!putTask.error) {
            NSLog(@"upload object success!");
            if (callback)   callback( YES,  @"上传成功", @[imageUrl]);
        } else {
            NSLog(@"upload object failed, error: %@" , putTask.error);
            if (callback)   callback( NO,  @"上传失败", nil);
        }
    }];
}
```
