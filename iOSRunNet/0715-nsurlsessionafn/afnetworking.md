# AFNetworking

### get-datatask-progress

```objc

- (void)get1
{
    AFHTTPSessionManager *mgr = [AFHTTPSessionManager manager];


    [mgr GET:@"http://120.25.226.186:32812/video"
    parameters:nil progress:^(NSProgress * _Nonnull downloadProgress) {

        NSLog(@"%f", 1.0 * downloadProgress.completedUnitCount / downloadProgress.totalUnitCount);

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {

        NSLog(@"%@====%@", task, responseObject);

    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {

        NSLog(@"%@", error);

    }];

}

```

### get-parameters-NSURLSessionDataTask

```objc

- (void)get2
{
    AFHTTPSessionManager *mgr = [AFHTTPSessionManager manager];

    NSDictionary *params = @{
                             @"username" : @"abc",
                             @"pwd" : @"plo",
                             };

    [mgr GET:@"http://120.25.226.186:32812/login" parameters:
     params progress:^(NSProgress * _Nonnull downloadProgress) {



    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {

        NSLog(@"%@", responseObject);

    } failure:^(NSURLSessionDataTask * _Nullable task,
                NSError * _Nonnull error) {



    }];

}

```

### post-parameters-datetask

```objc
- (void)postLogin{
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];

    NSString *urlstr = @"http://localhost/login.php";

    NSDictionary *dic = @{@"username":@"zhangsan",@"password":@"zhang"};

   [manager POST:urlstr parameters:dic progress:nil success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
       NSLog(@"%@ %@",responseObject,[responseObject class]);

   } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
       NSLog(@"%@",error);

   }];
}

```


### download`AFHTTPSessionManager-NSURLRequest-NSURLSessionDownloadTask`**resume**

```objc
- (void)download1
{
    AFHTTPSessionManager *mgr = [AFHTTPSessionManager manager];

    NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"http://120.25.226.186:32812/resources/images/minion_02.png"]];

   NSURLSessionDownloadTask *task = [mgr downloadTaskWithRequest:request progress:^(NSProgress * _Nonnull downloadProgress) {

        NSLog(@"%f", 1.0 * downloadProgress.completedUnitCount / downloadProgress.totalUnitCount);

    } destination:^NSURL * _Nonnull(NSURL * _Nonnull targetPath, NSURLResponse * _Nonnull response) {

        NSString *filePath = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).lastObject stringByAppendingPathComponent:response.suggestedFilename];

        return [NSURL fileURLWithPath:filePath];

    } completionHandler:^(NSURLResponse * _Nonnull response, NSURL * _Nullable filePath, NSError * _Nullable error) {

        NSLog(@"%@,", filePath);
        NSLog(@"%@", error);

    }];

    [task resume];
}

```


### upload-`POST:parameters:constructingBodyWithBlock:`

```objc

- (void)upload1
{

    AFHTTPSessionManager *mgr = [AFHTTPSessionManager manager];

    // upload-`POST:parameters:constructingBodyWithBlock:`


    [mgr POST:@"http://120.25.226.186:32812/upload" parameters:nil
    constructingBodyWithBlock:^(id<AFMultipartFormData>  _Nonnull formData) {

        // 一 , 最简单方法
//        [formData appendPartWithFileURL:[NSURL fileURLWithPath:@"/Users/apple/Desktop/path.m"] name:@"file" error:nil];

        // 二, 略微复杂
        [formData appendPartWithFileURL:[NSURL fileURLWithPath:@"/Users/apple/Desktop/path.m"] name:@"file" fileName:@"path.m" mimeType:@"text/plain" error:nil];

        // 三最复杂
        [formData appendPartWithFileData:[NSData dataWithContentsOfFile:@"/Users/apple/Desktop/path.txt"] name:@"file" fileName:@"test.txt" mimeType:@"text/plain"];

    } progress:^(NSProgress * _Nonnull uploadProgress) {

        NSLog(@"%f", 1.0 * uploadProgress.completedUnitCount / uploadProgress.totalUnitCount);

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {

        NSLog(@"%@", responseObject);

    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {

        NSLog(@"%@", error);

    }];

}

```

### https-json-xml-AFImageResponseSerializer-securityPolicy-acceptableContentTypes

```objc
- (void)https
{
    AFHTTPSessionManager *mgr = [AFHTTPSessionManager manager];

        // 使AFN支持HTTPS请求 : 2.5.4之前
//    mgr.securityPolicy.allowInvalidCertificates = YES;


        // 使AFN支持HTTPS请求 : 2.6.1以后
    mgr.securityPolicy.validatesDomainName = NO;


    // 修改AFN默认支持接收的文本类型
//    mgr.responseSerializer.acceptableContentTypes = [NSSet setWithObjects:@"application/json", @"text/json", @"text/javascript", @"text/html", nil];

    // 修改AFN默认处理数据的方式 : 设置成只返回原始的二进制数据,程序猿自己反序列化
//    mgr.responseSerializer = [AFHTTPResponseSerializer serializer];

    // xml自动反序列化, 返回一个xml-Parser
    mgr.responseSerializer = [AFXMLParserResponseSerializer serializer];

    // json自动反序列化
//    mgr.responseSerializer = [AFJSONResponseSerializer serializer];

    // imageData自动反序列化
//    mgr.responseSerializer = [AFImageResponseSerializer serializer];

    [mgr GET:@"http://120.25.226.186:32812/video" parameters:@{@"type" : @"XML"} progress:^(NSProgress * _Nonnull downloadProgress) {

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {

        NSLog(@"%@", responseObject);
//        NSLog(@"%@", [[NSString alloc] initWithData:responseObject encoding:NSUTF8StringEncoding]);


    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {

    }];
}

```

### `AFNetworkReachabilityManager`网络监控

```objc

- (void)netWork
{
    // 1.创建网络监测单例
    AFNetworkReachabilityManager *manager = [AFNetworkReachabilityManager sharedManager];
    /*
     status
     AFNetworkReachabilityStatusUnknown          = -1, 不知道监测的是什么
     AFNetworkReachabilityStatusNotReachable     = 0,  没有检测到网络
     AFNetworkReachabilityStatusReachableViaWWAN = 1,  蜂窝网
     AFNetworkReachabilityStatusReachableViaWiFi = 2,  WIFI
     */
    // 2.实现网络监测的回调
    [manager setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
         NSLog(@"%zd",status);
    }];
    // 3.开始监测
    [manager startMonitoring];
}

```
