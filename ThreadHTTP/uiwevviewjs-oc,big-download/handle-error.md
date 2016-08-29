# handle-ERROR

##main.m

```objc

int main(int argc, char * argv[]) {
//    @try {
        @autoreleasepool {
            return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
        }
//    } @catch (NSException *exception) {
//        NSLog(@"main------%@", [exception callStackSymbols]);
//    }
}

```

## Application.m

```objc

/**
 * 崩溃统计
 * 1.友盟
 * 2.Flurry
 * 3.Crashlytics
 */

/**
 * 拦截异常
 */
void handleException(NSException *exception)
{
    NSMutableDictionary *info = [NSMutableDictionary dictionary];
    info[@"callStack"] = [exception callStackSymbols]; // 调用栈信息（错误来源于哪个方法）
    info[@"name"] = [exception name]; // 异常名字
    info[@"reason"] = [exception reason]; // 异常描述（报错理由）
    //    [info writeToFile:<#(NSString *)#> atomically:<#(BOOL)#>];
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    // 将沙盒中的错误信息传递给服务器

    // 设置捕捉异常的回调
    NSSetUncaughtExceptionHandler(handleException);

    return YES;
}

```


### 程序不死

```objc
void handleException(NSException *exception)
{
    [[UIApplication sharedApplication].delegate performSelector:@selector(handle)];
}

- (void)handle
{
    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"哈哈" message:@"傻逼了把" delegate:self cancelButtonTitle:@"好的" otherButtonTitles:nil, nil];
    [alertView show];

    // 重新启动RunLoop
    [[NSRunLoop currentRunLoop] addPort:[NSPort port] forMode:NSDefaultRunLoopMode];
    [[NSRunLoop currentRunLoop] run];
}

- (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
{
    NSLog(@"-------点击了好的");
    exit(0);
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    // 设置捕捉异常的回调
    NSSetUncaughtExceptionHandler(handleException);

    return YES;
}


```
