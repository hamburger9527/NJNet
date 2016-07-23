# https:-code

```objc

@interface ViewController () <NSURLSessionTaskDelegate>

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    NSURLSession *session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[[NSOperationQueue alloc] init]];

    NSURLSessionDataTask *task = [session dataTaskWithURL:[NSURL URLWithString:@"https://www.apple.com/"] completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
        NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
    }];

    [task resume];
}

#pragma mark - <NSURLSessionTaskDelegate>
/**
 * challenge ： 挑战、质询
 * completionHandler : 通过调用这个block，来告诉URLSession要不要接收这个证书
 */
- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task
didReceiveChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void
(^)(NSURLSessionAuthChallengeDisposition, NSURLCredential *))completionHandler
{
    // 如果不是服务器信任类型的证书，直接返回
    if (![challenge.protectionSpace.authenticationMethod isEqualToString:NSURLAuthenticationMethodServerTrust]) return;

    // void (^)(NSURLSessionAuthChallengeDisposition, NSURLCredential *)
    // NSURLSessionAuthChallengeDisposition ： 如何处理这个安全证书
    // NSURLCredential ：安全证书

    // 1,根据服务器的信任信息创建证书对象
//    NSURLCredential *crdential = [NSURLCredential credentialForTrust:challenge.protectionSpace.serverTrust];

    // 利用这个block说明使用这个证书
//    if (completionHandler) {
//        completionHandler(NSURLSessionAuthChallengeUseCredential, crdential);
//    }

    //2,直接在质询里边拿到建议的证书
    !completionHandler ? : completionHandler(NSURLSessionAuthChallengeUseCredential, challenge.proposedCredential);
}

@end


```
