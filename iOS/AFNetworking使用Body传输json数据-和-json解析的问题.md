###一、关于josn解析价格相关的问题

#### 1. json解析精度丢失  s: 3.0  Android ： 3.0  IOS ：2.999999
原因：苹果系统默认是双精度 ， 系统默认的NSJSONSerialization解析出来是double导致
解决：
1.服务返回字符串类型 
2.使用系统NSDecimalNumber类处理
           
 ```
/**
 处理json float double 精度丢失问题
 */
- (NSString *)decimalNumber {
    NSString *doubleString  = [NSString stringWithFormat:@"%lf", self.doubleValue]; 
    NSDecimalNumber *decNumber    = [NSDecimalNumber decimalNumberWithString:doubleString];
    return [decNumber stringValue];
}
```
### 二、 NSJSONSerialization 使用
2.1 NSJSONSerialization提供了将JSON数据转换为Foundation对象（一般都是NSDictionary和NSArray）和Foundation对象转换为JSON数据（可以通过调用isValidJSONObject来判断Foundation对象是否可以转换为JSON数据）

2.2 NSJSONReadingOptions 
```
typedef NS_OPTIONS(NSUInteger, NSJSONReadingOptions) 
{
    // 可变的容器 数组或字典 
    NSJSONReadingMutableContainers = (1UL << 0), 

    // 指定在JSON对象可变字符串被创建为NSMutableString的实例 
    NSJSONReadingMutableLeaves = (1UL << 1),  

    // 允许不属于的NSArray或NSDictionary中的实例顶层对象 ，碎片化的json数据
    NSJSONReadingAllowFragments = (1UL << 2) 
} 
```

2.3 NSJSONWritingOptions 
```
typedef NS_OPTIONS(NSUInteger, NSJSONWritingOptions)
 {
    // 将生成的json数据格式化输出，不设置则输出的json字符串就是一整行。
    NSJSONWritingPrettyPrinted = (1UL << 0),

    /* Sorts dictionary keys for output using [NSLocale systemLocale]. Keys are compared using NSNumericSearch. The specific sorting method used is subject to change.
     */
    NSJSONWritingSortedKeys,
}
```

2.4 一般业务中网络请求json解析， 使用第三方解析非常方便，
OC： YYModel  MJExtension  Mantle 等；
Swift:： ObjectMapper  HandyJSON SwiftyJSON
```
/**
 *  json转字符串
 */
+ (NSString *)jsonToString:(id)data {
    if(data == nil) { return nil; }
    NSData *jsonData = [NSJSONSerialization dataWithJSONObject:data options:NSJSONWritingPrettyPrinted error:nil];
    return [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];
}
``` 

###三、AFNetworking 使用Body传输json数据

```

/// 使用body传数据
+ (NSURLSessionTask *)postBodyWithApi:(NSString *)api json:(id)json callback:(APICallback)callback {
    
    // url
    NSString *urlstr = [kApiPrefix stringByAppendingString:api];

    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    manager.requestSerializer.timeoutInterval = 30;

    // 设置header
    [manager.requestSerializer setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    XKTokenModel  *tokenModel = [YMCacheHelper sharedCacheHelper].tokenModel;
    NSString *header = [NSString stringWithFormat:@"%@ %@",tokenModel.token_type, tokenModel.access_token];
    [manager.requestSerializer setValue:header forHTTPHeaderField:@"Authorization"];


    // request
    NSError *requestError = nil; 
    NSMutableURLRequest *request = [manager.requestSerializer requestWithMethod:@"POST" URLString:urlstr parameters:nil error:&requestError];
    
    // body
    NSData *postData = [json dataUsingEncoding:NSUTF8StringEncoding]; 
    [request setHTTPBody:postData];
    
    NSLog(@"\n请求地址: %@ \n请求头:%@", urlstr,manager.requestSerializer.HTTPRequestHeaders);
    NSLog(@"请求参数:%@ \n",  json);
    
    NSURLSessionDataTask *dataTask = [manager.session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) { 
        
        if (error) { // failed
            NSLog("\n请求失败：%@", error);
            if (callback) callback(nil,kLoadError,error);
        }
        else 
        { // succese
            NSError * error = nil;
            id responseObject = [NSJSONSerialization JSONObjectWithData:data options:NSJSONReadingMutableContainers error:&error];
            NSInteger code = [responseObject[kServerCode] integerValue];
            NSString *msg = responseObject[kServerMsg];
            
            if (error) {
                NSLog("\n解析数据失败：%@", responseObject);
                if (callback) callback(nil,kTransformError,error);
            }
            else if ( code!= kSuccesCode) {
                error = [NSError errorWithDomain:urlstr code:code userInfo:nil];
                if (callback) callback(nil,msg,error);
            }
            else {
                NSError *error = nil;
                id data = [self parseResponse:responseObject domain:urlstr error:&error];   
                NSLog("\n请求成功 数据：\n%@", responseObject);
                if ([NSThread isMainThread]) {
                    if (code == 10400) {
                        if (callback) callback(nil,data[kServerMsg],error);
                    }else {
                        if (callback) callback(data,data[kServerMsg],nil);
                    }
                    
                }else {
                    dispatch_async(dispatch_get_main_queue(), ^{
                        if (callback) callback(data,data[kServerMsg],nil);
                    });
                }
                
            }
        }
        
    }];
    [dataTask resume];
    return dataTask;
}
```




