# oc调js

```objc

#pragma mark - <UIWebViewDelegate>
- (void)webViewDidFinishLoad:(UIWebView *)webView
{
    [webView stringByEvaluatingJavaScriptFromString:@"alert(100);"];

    // 利用JS获得当前网页的标题
    self.title = [webView stringByEvaluatingJavaScriptFromString:@"document.title;"];

    NSString *result = [webView stringByEvaluatingJavaScriptFromString:@"login();"];
    NSLog(@"%@", result);
}

```
