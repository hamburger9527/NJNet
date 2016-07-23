# NSURLConnection&RunLoop

## 只有让子线程的runloop跑起来, 才能和其他子线程的runloop不断的相互交流, 主线程的runloop默认已经跑起来了

```objc

@interface ViewController () <NSURLConnectionDataDelegate>
/** runLoop */
@property (nonatomic, assign) CFRunLoopRef runLoop;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSURLConnection *conn = [NSURLConnection connectionWithRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://120.25.226.186:32812/resources/images/minion_01.png"]] delegate:self];
        // 决定代理方法在哪个队列中执行
        [conn setDelegateQueue:[[NSOperationQueue alloc] init]];

        // 决定了让runloop一直跑
        [[NSRunLoop currentRunLoop] addPort:[NSPort port] forMode:NSDefaultRunLoopMode];
        // 启动子线程的runLoop
//        [[NSRunLoop currentRunLoop] run];

        self.runLoop = CFRunLoopGetCurrent();

        // 启动runLoop
        CFRunLoopRun();
    });
}

#pragma mark - <NSURLConnectionDataDelegate>
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response
{
    NSLog(@"didReceiveResponse----%@", [NSThread currentThread]);
}

- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data
{

    NSLog(@"didReceiveData----%@", [NSThread currentThread]);
}

- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
    NSLog(@"connectionDidFinishLoading----%@", [NSThread currentThread]);

    // 停止RunLoop
    CFRunLoopStop(self.runLoop);
}

@end

```
