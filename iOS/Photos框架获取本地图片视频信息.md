![image](http://upload-images.jianshu.io/upload_images/2051176-74ef03746b021aee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://upload-images.jianshu.io/upload_images/2051176-a23d65e784127157.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


####1.PHAsset获取本地图片的信息：名称，大小
```
// 名称
PHAsset* asset;
NSString *fileName = @"";
if (asset) {
    fileName = [asset valueForKey:@"filename"];
}

// 大小
UIImage* image；
NSData* data = UIImageJPEGRepresentation(image, 1);
NSInteger size = [data length];
```

####2.对于拍照 获取本地图片的信息：名称，大小

```
#pragma mark - UIImagePickerControllerDelegate

- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info {
    
    NSLog(@"%@", info);
    [picker dismissViewControllerAnimated:YES completion:nil];
    
    // info中就包含了选择的图片
    UIImage *image = info[UIImagePickerControllerOriginalImage];
    self.avatarImageView.image = image;
   
   // 先保存到相册 然后获取 PHAsset
  // saveImageToPhotoLibrary - 已经写YMImage分类
    self.asset = [image saveImageToPhotoLibrary][0];
}
```

#### YMImage分类
```
/**
 保存图片到本地，并读取本地图片信息
 
 相当于 - (PHFetchResult<PHAsset *> *)saveImageToPhotoLibrary
 */
- (void)saveImageToPhotoLibraryFinish:(void(^)(PHAsset *asset))finish {
    NSMutableArray *imageIds = [NSMutableArray array];
    [[PHPhotoLibrary sharedPhotoLibrary] performChanges:^{
        //写入图片到相册
        PHAssetChangeRequest *req = [PHAssetChangeRequest creationRequestForAssetFromImage:self];
        //记录本地标识，等待完成后取到相册中的图片对象
        [imageIds addObject:req.placeholderForCreatedAsset.localIdentifier];
    } completionHandler:^(BOOL success, NSError * _Nullable error) {
        if (success) {
            //成功后取相册中的图片对象
            PHFetchResult *result = [PHAsset fetchAssetsWithLocalIdentifiers:imageIds options:nil];
            if (finish) finish([result firstObject]);
        }
    }];
}

/**
 获得刚才添加到【相机胶卷】中的图片 
 
 相当于 - (void)saveImageToPhotoLibraryFinish:(void(^)(PHAsset *asset))finish
 */
- (PHFetchResult<PHAsset *> *)saveImageToPhotoLibrary {
    __block NSString *createdAssetId = nil;
    // 添加图片到【相机胶卷】
    [[PHPhotoLibrary sharedPhotoLibrary] performChangesAndWait:^{
        createdAssetId = [PHAssetChangeRequest creationRequestForAssetFromImage:self].placeholderForCreatedAsset.localIdentifier;
    } error:nil];
    if (createdAssetId == nil) return nil;
    // 在保存完毕后取出图片
    return [PHAsset fetchAssetsWithLocalIdentifiers:@[createdAssetId] options:nil];
}
```

####3.选择视频
```
#import <MobileCoreServices/MobileCoreServices.h>

[self openImagePickerController:UIImagePickerControllerSourceTypePhotoLibrary];

- (void)openImagePickerController:(UIImagePickerControllerSourceType)type
{
    if (![UIImagePickerController isSourceTypeAvailable:type]) return;
    
    UIImagePickerController *ipc = [[UIImagePickerController alloc] init];
    ipc.sourceType = type;
    ipc.mediaTypes = [NSArray arrayWithObject:(__bridge NSString*)kUTTypeMovie];
    ipc.delegate = self;
    [self presentViewController:ipc animated:YES completion:nil];
}

#pragma mark - UIImagePickerControllerDelegate

- (void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info {
    
    NSLog(@"%@", info);
    
    NSString *mediaType = [info objectForKey:UIImagePickerControllerMediaType];
    if ([mediaType isEqualToString:(__bridge NSString*)kUTTypeMovie]){
        
        if (@available(iOS 11.0, *)) {
            PHAsset *asset = info[UIImagePickerControllerPHAsset];
            NSString *fileName = @"";
            if (asset) {
                fileName = [asset valueForKey:@"filename"];
                NSLog(@"%@", fileName);
            }
        } else {
            PHFetchResult *result = [PHAsset fetchAssetsWithMediaType:PHAssetMediaTypeVideo options:nil];
            [result enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
                NSString *fileName = [obj valueForKey:@"filename"];
                if (fileName.length) {
                    *stop = YES;
                }
                NSLog(@"%@", fileName);
            }];
            
        }
    }
    [picker dismissViewControllerAnimated:YES completion:nil];
}
```

####4.拍照后获取图片名字 和 图片路径

```

// 保存图片到相册
- (void)saveImageToPhotoLibrary:(UIImage *)image completed:(void(^)( NSString * _Nullable imageURL))completed {

    // 1. 获取当前App的相册授权状态
    PHAuthorizationStatus authorizationStatus = [PHPhotoLibrary authorizationStatus];
    // 2. 判断授权状态
    if (authorizationStatus == PHAuthorizationStatusAuthorized) {
        __block NSString *createdAssetId = nil;
        // 添加图片到【相机胶卷】
        [[PHPhotoLibrary sharedPhotoLibrary] performChangesAndWait:^{
            createdAssetId = [PHAssetChangeRequest creationRequestForAssetFromImage:image].placeholderForCreatedAsset.localIdentifier;
        } error:nil];

        if (createdAssetId == nil) {
            !completed ? : completed(nil);
        }

        // 在保存完毕后取出图片信息
        PHFetchResult * result = [PHAsset fetchAssetsWithLocalIdentifiers:@[createdAssetId] options:nil];
        PHAsset *asset = [result firstObject];

        [result enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {

            NSString *fileName = [obj valueForKey:@"filename"];
            NSLog(@"%@", fileName); // 能得到名字 IMG_3301.JPG
        }];

        [asset requestContentEditingInputWithOptions:nil completionHandler:^(PHContentEditingInput * _Nullable contentEditingInput, NSDictionary * _Nonnull info) {

            NSLog(@"URL: %@",  contentEditingInput.fullSizeImageURL.absoluteString);
            NSString* path = [contentEditingInput.fullSizeImageURL.absoluteString substringFromIndex:7];//screw all the crap of file://
            NSLog(@"path: %@", path);
            NSFileManager *fileManager = [NSFileManager defaultManager];
            BOOL isExist = [fileManager fileExistsAtPath:path];
            if (isExist) {
                NSLog(@"oh yeah");
                !completed ? : completed(path);
            }
            else {
                NSLog(@"damn");
                !completed ? : completed(nil);
            }
        }];

    }
    else if (authorizationStatus == PHAuthorizationStatusNotDetermined) { // 如果没决定, 弹出指示框, 让用户选择
        [PHPhotoLibrary requestAuthorization:^(PHAuthorizationStatus status) {
            // 如果用户选择授权, 则保存图片
            if (status == PHAuthorizationStatusAuthorized) {
                [self saveImageToPhotoLibrary:image completed:completed];
            }
        }];
    } else {
        NSLog(@"请在设置界面, 授权访问相册");
        completed(nil);
    }
}
```
