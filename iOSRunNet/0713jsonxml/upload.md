# upLoad

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/05-文件上传下载/Snip20160705_3.png)


![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/05-文件上传下载/Snip20160705_4.png)

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/05-文件上传下载/Snip20160704_5.png)


```objc


#define XMGBoundary @"520it"
#define XMGEncode(string) [string dataUsingEncoding:NSUTF8StringEncoding]
#define XMGNewLine [@"\r\n" dataUsingEncoding:NSUTF8StringEncoding]
    // 创建请求
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/upload"];
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    request.HTTPMethod = @"POST";

    // 设置请求头(告诉告诉服务器,这是一个文件上传的请求)
    [request setValue:[NSString stringWithFormat:@"multipart/form-data; boundary=%@", XMGBoundary] forHTTPHeaderField:@"Content-Type"];

    // 设置请求体
    NSMutableData *body = [NSMutableData data];

    // 文件参数
    /*
     --分割线\r\n
     Content-Disposition: form-data; name="参数名"; filename="文件名"\r\n
     Content-Type: 文件的MIMEType\r\n
     \r\n
     文件数据
     \r\n
     */
    // 分割线
    [body appendData:XMGEncode(@"--")];
    [body appendData:XMGEncode(XMGBoundary)];
    [body appendData:XMGNewLine];

    // 文件参数名
    [body appendData:XMGEncode([NSString stringWithFormat:@"Content-Disposition: form-data; name=\"file\"; filename=\"test.png\""])];
    [body appendData:XMGNewLine];

    // 文件的类型
    [body appendData:XMGEncode([NSString stringWithFormat:@"Content-Type: image/png"])];
    [body appendData:XMGNewLine];



    // 文件数据
    [body appendData:XMGNewLine];
//    UIImageJPEGRepresentation(<#UIImage *image#>, <#CGFloat compressionQuality#>)
    UIImage *image = [UIImage imageNamed:@"placeholder"];
    [body appendData:UIImagePNGRepresentation(image)];
//    [body appendData:[NSData dataWithContentsOfFile:@"/Users/xiaomage/Desktop/test.png"]];
    [body appendData:XMGNewLine];

    // 非文件参数
    /*
     --分割线\r\n
     Content-Disposition: form-data; name="参数名"\r\n
     \r\n
     参数值
     \r\n
     */
    // 分割线
    [body appendData:XMGEncode(@"--")];
    [body appendData:XMGEncode(XMGBoundary)];
    [body appendData:XMGNewLine];

    // 参数名
    [body appendData:XMGEncode([NSString stringWithFormat:@"Content-Disposition: form-data; name=\"username\""])];
    [body appendData:XMGNewLine];

    // 参数值
    [body appendData:XMGNewLine];
    [body appendData:XMGEncode(@"jack")];
    [body appendData:XMGNewLine];

    // 结束标记
    /*
     --分割线--\r\n
     */
    [body appendData:XMGEncode(@"--")];
    [body appendData:XMGEncode(XMGBoundary)];
    [body appendData:XMGEncode(@"--")];
    [body appendData:XMGNewLine];

    request.HTTPBody = body;

    [NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {
        NSLog(@"%@", [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:nil]);
    }];
}


```
