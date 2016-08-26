# statusBarHUD

- 版本号
    - 大版本号.功能更新版本号.BUG修复版本号
    - 0.0.0

- 窗口的层级
    - alert的等级最高

```objc
UIKIT_EXTERN const UIWindowLevel UIWindowLevelNormal; 0
UIKIT_EXTERN const UIWindowLevel UIWindowLevelAlert; 2
UIKIT_EXTERN const UIWindowLevel UIWindowLevelStatusBar __TVOS_PROHIBITED; 1

```

- 能拿到状态条的frame**[UIApplication sharedApplication].statusBarFrame**

- 打包程序中的资源**"LMJStatusBarHUD.bundle/success"**


### LMJStatusBar `.h`

```objc

@interface LMJStatusBarHUD : NSObject

/**
 * 显示普通信息
 * @param msg       文字
 * @param image     图片 或者 view
 */
+ (void)showMessage:(NSString *)msg imageOrView:(id)image;
/**
 * 显示普通信息
 */
+ (void)showMessage:(NSString *)msg;
/**
 * 显示成功信息
 */
+ (void)showSuccess:(NSString *)msg;
/**
 * 显示失败信息
 */
+ (void)showError:(NSString *)msg;
/**
 * 显示正在处理的信息
 */
+ (void)showLoading:(NSString *)msg;
/**
 * 隐藏
 */
+ (void)hide;

@end

```

### LMJStatusBar `.m`


```objc

#import "LMJStatusBarHUD.h"

#define LMJStatusBarFrame [UIApplication sharedApplication].statusBarFrame

static CGFloat const LMJWindowAnimationDuration = 0.25;
static CGFloat const LMJStatusHoldTime = 2;
static UIWindow *window_ = nil;
static NSTimer *timer_ = nil;

@implementation LMJStatusBarHUD


+ (void)showWindow
{
    window_.hidden = YES;
    window_ = nil;
//    NSLog(@"%@", NSStringFromCGRect(LMJStatusBarFrame));

    window_ = [[UIWindow alloc] initWithFrame:LMJStatusBarFrame];
    window_.windowLevel = UIWindowLevelStatusBar;
    window_.hidden = NO;
    window_.backgroundColor = [UIColor darkGrayColor];

    window_.transform = CGAffineTransformMakeTranslation(0, - LMJStatusBarFrame.size.height);

    [UIView animateWithDuration:LMJWindowAnimationDuration animations:^{

        window_.transform = CGAffineTransformIdentity;

    }];
}


+ (void)showMessage:(NSString *)msg imageOrView:(id)image
{
    // 停止之前的定时器
    [timer_ invalidate];
//    timer_ = nil;

    //开启一个新的定时器
    timer_ = [NSTimer scheduledTimerWithTimeInterval:LMJStatusHoldTime target:self selector:@selector(hide) userInfo:nil repeats:NO];

    [self showWindow];

    CGFloat windowW = LMJStatusBarFrame.size.width;

    UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
    btn.titleLabel.font = [UIFont systemFontOfSize:12];

    [window_ addSubview:btn];
    btn.userInteractionEnabled = NO;
    btn.frame = LMJStatusBarFrame;

    [btn setTitle:msg forState:UIControlStateNormal];

    if([image isKindOfClass:[UIImage class]])
    {
        [btn setImage:image forState:UIControlStateNormal];
        btn.titleEdgeInsets = UIEdgeInsetsMake(0, 10, 0, 0);
    }

    if([image isKindOfClass:[UIView class]])
    {
        btn.titleEdgeInsets = UIEdgeInsetsMake(0, 10, 0, 0);

        CGRect frame = [image frame];
        [window_ addSubview:image];

        CGFloat textW = [msg sizeWithAttributes:@{NSFontAttributeName : [UIFont systemFontOfSize:12]}].width;
        CGFloat viewX = (windowW - textW) / 2 - frame.size.width - 10;


        frame.origin.x = viewX;
        frame.origin.y = (LMJStatusBarFrame.size.height - frame.size.height)/2;
        [image setFrame:frame];
    }

    // 如果是活动指示器就把timer干掉
    if([image isKindOfClass:[UIActivityIndicatorView class]])
    {
        [timer_ invalidate];
        timer_ = nil;
    }

}

+ (void)showLoading:(NSString *)msg
{

    UIActivityIndicatorView *loadingView = [[UIActivityIndicatorView alloc] initWithActivityIndicatorStyle:UIActivityIndicatorViewStyleWhite];
    [loadingView startAnimating];
    [self showMessage:msg imageOrView:loadingView];
}

+ (void)showSuccess:(NSString *)msg
{
    [self showMessage:msg imageOrView:[UIImage imageNamed:@"LMJStatusBarHUD.bundle/success"]];
}

+ (void)showError:(NSString *)msg
{
    [self showMessage:msg imageOrView:[UIImage imageNamed:@"LMJStatusBarHUD.bundle/error"]];
}

+ (void)showMessage:(NSString *)msg
{
    [self showMessage:msg imageOrView:nil];
}

+ (void)hide
{
    [UIView animateWithDuration:LMJWindowAnimationDuration animations:^{
        window_.transform = CGAffineTransformMakeTranslation(0, - LMJStatusBarFrame.size.height);

    } completion:^(BOOL finished) {

        [timer_ invalidate];
        timer_ = nil;

        window_.hidden = YES;
        window_ = nil;
    }];
}

@end


```
