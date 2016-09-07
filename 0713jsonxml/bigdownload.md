# bigDownload


## NSFileHandle的使用
- 1,要创建一个空的文件,
- 2,创建句柄对象
- 3,移动到最后边
- 4,写入数据
- 5,关闭句柄

```objc
    // 创建一个空的文件
    [[NSFileManager defaultManager] createFileAtPath:XMGFile contents:nil attributes:nil];

    // 创建文件句柄
    self.handle = [NSFileHandle fileHandleForWritingAtPath:XMGFile];

    // 指定数据的写入位置 -- 文件内容的最后面
    [self.handle seekToEndOfFile];

    // 写入数据
    [self.handle writeData:data];

        // 关闭handle
    [self.handle closeFile];
    self.handle = nil;

```

## demo

```objc
@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/resources/videos/minion_15.mp4"];
    [NSURLConnection connectionWithRequest:[NSURLRequest requestWithURL:url] delegate:self];
}

#pragma mark - <NSURLConnectionDataDelegate>
/**
 * 接收到响应的时候：创建一个空的文件
 */
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSHTTPURLResponse *)response
{
    // 获得文件的总长度
    self.contentLength = [response.allHeaderFields[@"Content-Length"] integerValue];

    // 创建一个空的文件
    [[NSFileManager defaultManager] createFileAtPath:XMGFile contents:nil attributes:nil];

    // 创建文件句柄
    self.handle = [NSFileHandle fileHandleForWritingAtPath:XMGFile];
}

/**
 * 接收到具体数据：马上把数据写入一开始创建好的文件
 */
- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data
{
    // 指定数据的写入位置 -- 文件内容的最后面
    [self.handle seekToEndOfFile];

    // 写入数据
    [self.handle writeData:data];

    // 拼接总长度
    self.currentLength += data.length;

    // 进度
    self.progressView.progress = 1.0 * self.currentLength / self.contentLength;
}

- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
    // 关闭handle
    [self.handle closeFile];
    self.handle = nil;

    // 清空长度
    self.currentLength = 0;
}

@end

```
