# webViewDelegate

```objc

@interface ViewController () <UIWebViewDelegate>
@property (weak, nonatomic) IBOutlet UIWebView *webView;
@property (weak, nonatomic) IBOutlet UIBarButtonItem *backItem;
@property (weak, nonatomic) IBOutlet UIBarButtonItem *forward;
@end

@implementation ViewController
- (IBAction)back:(id)sender {
    [self.webView goBack];
}

- (IBAction)forward:(id)sender {
    [self.webView goForward];
}

- (IBAction)refresh:(id)sender {
    [self.webView reload];
}

- (void)viewDidLoad {
    [super viewDidLoad];

    // Native(OC+Swift) + HTML5

    self.webView.delegate = self;
    // 网页内容缩小到适应整个设备屏幕
//    self.webView.scalesPageToFit = YES;

    // 检测各种特殊的字符串：比如电话、网站
    self.webView.dataDetectorTypes = UIDataDetectorTypeAll;

    [self.webView loadRequest:[NSURLRequest requestWithURL:[[NSBundle mainBundle] URLForResource:@"test" withExtension:@"html"]]];

//    [self.webView loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://www.520it.com"]]];

//    [self.webView loadRequest:[NSURLRequest requestWithURL:[NSURL fileURLWithPath:@"/Users/xiaomage/Desktop/test.pptx"]]];

//    [self.webView loadData:<#(NSData *)#> MIMEType:<#(NSString *)#> textEncodingName:<#(NSString *)#> baseURL:<#(NSURL *)#>];

//    [self.webView loadHTMLString:@"<html><body><div style=\"color: red; font-size:10px; border:1px solid blue;\">哈哈哈哈哈</div></body></html>" baseURL:nil];

    self.webView.scrollView.contentInset = UIEdgeInsetsMake(20, 0, 0, 0);
}

#pragma mark - <UIWebViewDelegate>
- (void)webViewDidFinishLoad:(UIWebView *)webView
{
//    NSLog(@"%s", __func__);

    self.backItem.enabled = webView.canGoBack;
    self.forward.enabled = webView.canGoForward;
}

- (void)webViewDidStartLoad:(UIWebView *)webView
{
//    NSLog(@"%s", __func__);
}

- (void)webView:(UIWebView *)webView didFailLoadWithError:(NSError *)error
{
//    NSLog(@"%s", __func__);

    self.backItem.enabled = webView.canGoBack;
    self.forward.enabled = webView.canGoForward;
}

/**
 * 每当webView即将发送一个请求之前，都会调用这个方法
 * 返回YES：允许加载这个请求
 * 返回NO：禁止加载这个请求
 */
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
//    NSLog(@"%@", request.URL);
    if ([request.URL.absoluteString containsString:@"life"]) return NO;

    // JS  JavaScript
    return YES;
}

```
