# NSURLSession-大文件断点下载

## 大文件断点下载`不退出应用程序` `suspend` `resume`

```objc

@interface ViewController () <NSURLSessionDownloadDelegate>
/** 下载任务 */
@property (nonatomic, strong) NSURLSessionDownloadTask *task;
@end

@implementation ViewController
/**
 * 开始下载
 */
- (IBAction)start:(id)sender {
    if(self.task == nil)
    {
        // 获得NSURLSession对象
        NSURLSession *session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[[NSOperationQueue alloc] init]];

        // 获得下载任务
        self.task = [session downloadTaskWithURL:[NSURL URLWithString:@"http://120.25.226.186:32812/resources/videos/minion_01.mp4"]];

        // 启动任务
        [self.task resume];
    }
}

/**
 * 暂停下载
 */
- (IBAction)pause:(id)sender {
    if(self.task.state == NSURLSessionTaskStateRunning)
    {
        [self.task suspend];
    }
}

/**
 * 继续下载
 */
- (IBAction)goOn:(id)sender {
    if(self.task.state == NSURLSessionTaskStateSuspended)
    {
        [self.task resume];
    }
}

#pragma mark - <NSURLSessionDownloadDelegate>
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
{
    NSLog(@"didCompleteWithError");
}

- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didResumeAtOffset:(int64_t)fileOffset expectedTotalBytes:(int64_t)expectedTotalBytes
{
    NSLog(@"didResumeAtOffset");
}

/**
 * 每当写入数据到临时文件时，就会调用一次这个方法
 * totalBytesExpectedToWrite:总大小
 * totalBytesWritten: 已经写入的大小
 * bytesWritten: 这次写入多少
 */
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didWriteData:(int64_t)bytesWritten totalBytesWritten:(int64_t)totalBytesWritten totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite
{
    NSLog(@"--------%f", 1.0 * totalBytesWritten / totalBytesExpectedToWrite);
}

/**
 *
 * 下载完毕就会调用一次这个方法
 */
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)location
{
    // 文件将来存放的真实路径
    NSString *file = [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:downloadTask.response.suggestedFilename];

    // 剪切location的临时文件到真实路径
    NSFileManager *mgr = [NSFileManager defaultManager];
    [mgr moveItemAtURL:location toURL:[NSURL fileURLWithPath:file] error:nil];
}


```


## 大文件断点下载`不退出应用程序` `cancelByProducingResumeData``downloadTaskWithResumeData`

```objc

// resumeData的文件路径
#define XMGResumeDataFile [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:@"resumeData.tmp"]

#import "ViewController.h"

@interface ViewController () <NSURLSessionDownloadDelegate>
/** 下载任务 */
@property (nonatomic, strong) NSURLSessionDownloadTask *task;
/** 保存上次的下载信息 */
@property (nonatomic, strong) NSData *resumeData;

/** session */
@property (nonatomic, strong) NSURLSession *session;
@end

@implementation ViewController

- (NSURLSession *)session
{
    if (!_session) {
        _session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[[NSOperationQueue alloc] init]];
    }
    return _session;
}

/**
 * 开始下载
 */
- (IBAction)start:(id)sender {

    if(self.task == nil && self.resumeData == nil)
    {

        self.task = [self.session downloadTaskWithURL:[NSURL URLWithString:@"http://120.25.226.186:32812/resources/videos/minion_01.mp4"]];

        [self.task resume];

    }

    // 启动任务
    [self.task resume];
}

/**
 * 暂停下载
 */
- (IBAction)pause:(id)sender {
    // 一旦这个task被取消了，就无法再恢复
    if(self.task != nil && self.resumeData == nil)
    {
        [self.task cancelByProducingResumeData:^(NSData * _Nullable resumeData) {

            self.resumeData = resumeData;
            self.task = nil;
        }];
    }

}

/**x
 请求这个路径：http://120.25.226.186:32812/resources/videos/minion_01.mp4
 设置请求头
 Range : 1024-2000
 */
/**
 * 继续下载
 */
- (IBAction)goOn:(id)sender {

    if(self.task == nil && self.resumeData)
    {
        self.task = [self.session downloadTaskWithResumeData:self.resumeData];
        [self.task resume];
        self.resumeData = nil;
    }
}

#pragma mark - <NSURLSessionDownloadDelegate>
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
{
    NSLog(@"didCompleteWithError");

    // 保存恢复数据
    if(error)
    {
        self.resumeData = error.userInfo[NSURLSessionDownloadTaskResumeData];
        self.task = nil;
    }
}

- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didResumeAtOffset:(int64_t)fileOffset expectedTotalBytes:(int64_t)expectedTotalBytes
{
    NSLog(@"didResumeAtOffset");
}

/**
 * 每当写入数据到临时文件时，就会调用一次这个方法
 * totalBytesExpectedToWrite:总大小
 * totalBytesWritten: 已经写入的大小
 * bytesWritten: 这次写入多少
 */
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didWriteData:(int64_t)bytesWritten totalBytesWritten:(int64_t)totalBytesWritten totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite
{
    NSLog(@"--------%f", 1.0 * totalBytesWritten / totalBytesExpectedToWrite);
}


/**
 *
 * 下载完毕就会调用一次这个方法
 */
- (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)location
{
    // 文件将来存放的真实路径
    NSString *file = [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:downloadTask.response.suggestedFilename];

    // 剪切location的临时文件到真实路径
    NSFileManager *mgr = [NSFileManager defaultManager];
    [mgr moveItemAtURL:location toURL:[NSURL fileURLWithPath:file] error:nil];
}

@end


```
