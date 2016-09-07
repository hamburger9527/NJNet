# js调oc

```html
<html>
    <!-- 网页的描述信息 -->
    <head>
        <meta charset="UTF-8">
        <title>第一个网页</title>

        <script>
            function login()
            {
                // 让webView跳转到百度页面（加载百度页面）
                location.href = 'xmg://sendMessage_number2_number3_?100&100&99';
            }
        </script>
    </head>

    <!-- 网页的具体内容 -->
    <body>
        电话：10086
        <button style="background: red; width:100px; height:30px;" onclick="login();">Call</button>

        <br>
        <a href="http://www.baidu.com">百度</a>
    </body>
</html>

```

## 去除警告

```objc

// 去除Xcode编译警告
//#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-performSelector-leaks"
//#pragma clang diagnostic pop

```

## jc 调 OC

```objc

//#import <JavaScriptCore/JavaScriptCore.h>

@interface ViewController () <UIWebViewDelegate>
@property (weak, nonatomic) IBOutlet UIWebView *webView;

@end

@implementation ViewController

/**
 iOS : Xcode
 Java : eclipse\MyEclipse
 Android : eclipse\MyEclipse\Android Studio
 网页（前端）：Dreamweaver（美工）、Sublime（前端）、WebStorm（前端）

 iOS App == Native + HTML5

 完整的网页组成：
 HTML：内容（文字、图片）
 CSS：美化、样式
 JS：动态效果、事件处理、跟用户进行交互
 */

// 程序崩溃分析报告

- (void)viewDidLoad {
    [super viewDidLoad];
    [self.webView loadRequest:[NSURLRequest requestWithURL:[[NSBundle mainBundle] URLForResource:@"index" withExtension:@"html"]]];
}

- (void)sendMessage:(NSString *)number number2:(NSString *)number2 number3:(NSString *)number3
{
    NSLog(@"%s %@ %@ %@", __func__, number, number2, number3);
}

#pragma mark - <UIWebViewDelegate>
/**
 * 通过这个方法完成JS调用OC
 * JS和OC交互的第三方框架：WebViewJavaScriptBridge
 */
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    // url == xmg://sendMessage_?200
    NSString *url = request.URL.absoluteString;
    NSString *scheme = @"xmg://";
    if ([url hasPrefix:scheme]) {
        // 获得协议后面的路径  path == sendMessage_number2_?200&300
        NSString *path = [url substringFromIndex:scheme.length];
        // 利用?切割路径
        NSArray *subpaths = [path componentsSeparatedByString:@"?"];
        // 方法名 methodName == sendMessage:number2:
        NSString *methodName = [[subpaths firstObject] stringByReplacingOccurrencesOfString:@"_" withString:@":"];
        // 参数  200&300
        NSArray *params = nil;
        if (subpaths.count == 2) {
            params = [[subpaths lastObject] componentsSeparatedByString:@"&"];
        }
        // 调用
        [self performSelector:NSSelectorFromString(methodName) withObjects:params];

        return NO;
    }

    NSLog(@"想加载其他请求，不是想调用OC的方法");

    return YES;
}

@end

```
