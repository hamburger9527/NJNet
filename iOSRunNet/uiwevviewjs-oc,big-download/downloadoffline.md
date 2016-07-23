# DownLoadOffLineTask

- 用一个字典储存所有的文件大小
- 用NSOutputStream来写数据
- 在懒加载的时候从plist文件获得对应的文件大小
    - 如果没有得到, 就表示这个文件从来没有下载过
    - 如果得到了长度,
        - 但是文件长度`不等于`已经下载的文件大小, 就需要创建任务, 并且从已经下载的文件的大小处开始
        - 或者`等于`已经下载的文件大小, 就返回nil

- 在代理方法得到响应的时候,先判断在代理方法中有没有拿到需需要下载的文件总长度, 如果没有就说明,
  第一次下载该文件
    - 再拿到plist字典,
        - 如果那不到字典, 就说明是才安装程序, 需要创建字典
        - 再把第一次下载该文件的时候的总大小写进字典
        - 并把字典写入文件
- 下载的文件的当前的长度, 直接拿到文件的属性列表, 再去根据key获得

##`.h`

```objc
#import <UIKit/UIKit.h>


@interface LMJDownloadOffLineTask : NSObject

/** 返回sharedManager对象本身, 需要手动开始任务*/
+ (instancetype)downloadTaskWithURL:(NSString *)URLStr progress:(void(^)(CGFloat progress))progress complete:(void(^)(NSError *error, NSString *filePath))complete;
/**
 *  开始任务
 */
- (void)start;

/**
 *  暂停任务
 */
- (void)suspend;

@end

```

##`.m`

```objc

#import "LMJDownloadOffLineTask.h"
#import "NSString+Hash.h"

#define LMJTaskURL [NSURL URLWithString:_taskURL_String]

#define LMJFileName (_taskURL_String.md5String)

#define LMJFilePath ([NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).lastObject stringByAppendingPathComponent:LMJFileName])

#define LMJContentLengthDataDictPath ([NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).lastObject stringByAppendingPathComponent:(@"LMJ_CONTENT_DATA_CACHES_PLISTFILE_NAME".md5String)])

#define LMJFileCompleteLength [[[NSFileManager defaultManager] attributesOfItemAtPath:LMJFilePath error:nil][NSFileSize] integerValue]


@interface LMJDownloadOffLineTask ()<NSURLSessionDataDelegate>
/** session任务集合 */
@property (nonatomic, strong) NSURLSession *session;

/** <#digest#> */
@property (assign, nonatomic) NSString *taskURL_String;

/** 下载任务 */
@property (nonatomic, strong) NSURLSessionDataTask *task;

/** 文件写入的流 */
@property (nonatomic, strong) NSOutputStream *stream;

/** 文件的总长度 */
@property (assign, nonatomic) NSInteger contentLength;

/** 进度block */
@property (nonatomic, strong) void(^progressBlock)(CGFloat progress);

/** 完成blodck */
@property (nonatomic, strong) void(^completBlock)(NSError *error, NSString *filePath);

@end

@implementation LMJDownloadOffLineTask


- (NSURLSession *)session
{
    if(_session == nil)
    {
        _session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[[NSOperationQueue alloc] init]];
    }
    return _session;
}

- (NSOutputStream *)stream
{
    if(_stream == nil)
    {
        _stream = [NSOutputStream outputStreamToFileAtPath:LMJFilePath append:YES];
    }
    return _stream;
}

- (NSURLSessionDataTask *)task
{
    if(_task == nil)
    {
        // 先从写入的保存的字典中文件中获得当前需要下载文件的总大小
        self.contentLength = [[NSDictionary dictionaryWithContentsOfFile:LMJContentLengthDataDictPath][LMJFileName] integerValue];

        // 如果下载文件的大小==总大小, 就跳过,
        if(self.contentLength && self.contentLength == LMJFileCompleteLength)
        {
            self.completBlock(nil, LMJFilePath);

            return nil;
        }

        // 如果从来没有下载过contentlength == 0, 或者没有下载完,

        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:LMJTaskURL];

        // 就接着下载
        NSString *range = [NSString stringWithFormat:@"bytes=%zd-", LMJFileCompleteLength];

        [request setValue:range forHTTPHeaderField:@"Range"];

        _task = [self.session dataTaskWithRequest:request];
    }
    return _task;
}


- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveResponse:(NSURLResponse *)response completionHandler:(void (^)(NSURLSessionResponseDisposition))completionHandler
{
    [self.stream open];
    completionHandler(NSURLSessionResponseAllow);

    // 如果在懒加载中, 没有获得文件的总大小, 就表示第一次下载该文件
    if(self.contentLength == 0)
    {
        NSMutableDictionary *dictM = [NSMutableDictionary dictionaryWithContentsOfFile:LMJContentLengthDataDictPath];

        // 第一次运行程序, 没有plist文件
        if(dictM == nil) dictM = [NSMutableDictionary dictionary];

        self.contentLength = response.expectedContentLength;

        dictM[LMJFileName] = @(self.contentLength);

        // 写入文件
        [dictM writeToFile:LMJContentLengthDataDictPath atomically:YES];
    }
}

- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data
{
    [self.stream write:data.bytes maxLength:data.length];

    CGFloat progresspp = 1.0 * LMJFileCompleteLength / self.contentLength;


    //    NSLog(@"---%f---", progresspp);

    self.progressBlock(progresspp);
}


- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
{
    [self.stream close];

    self.stream = nil;

    self.task = nil;

    self.completBlock(error, LMJFilePath);
}

- (void)start
{
    if(self.task)
    {
        if(self.task.state == NSURLSessionTaskStateSuspended)
        {
            [self.task resume];
        }
    }
}

- (void)suspend
{
    if(_task)
    {
        if(_task.state == NSURLSessionTaskStateRunning)
        {
            [_task suspend];
        }
    }
}

+ (instancetype)downloadTaskWithURL:(NSString *)URLStr progress:(void(^)(CGFloat progress))progress complete:(void(^)(NSError *error, NSString *filePath))complete
{
    LMJDownloadOffLineTask *task = [[self alloc] init];

    task.taskURL_String = URLStr;

    task.progressBlock = progress;

    task.completBlock = complete;

    return task;
}

@end


```
