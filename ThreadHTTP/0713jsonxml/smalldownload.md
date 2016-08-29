# smallDownload

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/05-文件上传下载/Snip20160705_1.png)

## 使用NSURLConnection- delegate-<NSURLConnectionDataDelegate>

### 直接使用data

```objc
- (void)downLoad1
{
    NSLog(@"-------%@-------------11111---", [NSThread currentThread]);
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSLog(@"-------%@-------------3333333---", [NSThread currentThread]);
        NSData *data = [NSData dataWithContentsOfURL:[NSURL URLWithString:@"http://120.25.226.186:32812/resources/images/minion_11.png"]];
        UIImage *image = [UIImage imageWithData:data];
        dispatch_async(dispatch_get_main_queue(), ^{

            NSLog(@"-------%@-------------4444444---", [NSThread currentThread]);
            self.imageView.image = image;
        });

    });
    NSLog(@"-------%@-------------2222---", [NSThread currentThread]);
}

```

### 使用NSURLConnection

```objc

- (void)connDownload
{
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/resources/images/minion_12.png"];

    NSURLRequest *request = [NSURLRequest requestWithURL:url];

    NSLog(@"-------%@-------------11111---", [NSThread currentThread]);
    [NSURLConnection sendAsynchronousRequest:request queue:[[NSOperationQueue alloc] init] completionHandler:^(NSURLResponse * _Nullable response, NSData * _Nullable data, NSError * _Nullable connectionError) {
        NSLog(@"-------%@-------------3333333---", [NSThread currentThread]);
        UIImage *image = [UIImage imageWithData:data];

        [[NSOperationQueue mainQueue] addOperationWithBlock:^{
            self.imageView.image = image;
            NSLog(@"-------%@-------------4444444---", [NSThread currentThread]);
        }];

    }];
    NSLog(@"-------%@-------------2222---", [NSThread currentThread]);
}

```

### 使用NSURLConnection的代理

```objc

- (void)viewDidLoad {
    [super viewDidLoad];

    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/resources/videos/minion_15.mp4"];
    [NSURLConnection connectionWithRequest:[NSURLRequest requestWithURL:url] delegate:self];
}

#pragma mark - <NSURLConnectionDataDelegate>
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSHTTPURLResponse *)response
{
    self.contentLength = [response.allHeaderFields[@"Content-Length"] integerValue];
    self.fileData = [NSMutableData data];
}

- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data
{
    [self.fileData appendData:data];
    CGFloat progress = 1.0 * self.fileData.length / self.contentLength;
    NSLog(@"已下载：%.2f%%", (progress) * 100);
    self.progressView.progress = progress;
}

- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
    NSLog(@"下载完毕");

    // 将文件写入沙盒中

    // 缓存文件夹
    NSString *caches = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];

    // 文件路径
    NSString *file = [caches stringByAppendingPathComponent:@"minion_15.mp4"];

    // 写入数据
    [self.fileData writeToFile:file atomically:YES];
    self.fileData = nil;
}

```
