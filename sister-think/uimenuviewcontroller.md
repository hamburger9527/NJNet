# UIMenuViewController

### 1怎么让label显示出来`粘贴\剪切\复制`操作

- 1, 让label可以交互`self.userInteractionEnabled = YES`
- 2, 为label添加点击事件`addGestureRecognizer:`
- 3, 让label有资格成为第一响应者
- 4, label支持哪些操作
- 5, 点击的时候一定要调用`becomeFirstResponder`
- 6, 显示menuVc
    - 设置MenuController需要指向的矩形框
    - 设置会以targetView的左上角为坐标原点
    - setMenuVisible == YES

- 7, 剪切的时候用到pasteBoard

```objc

@implementation XMGLabel

- (void)awakeFromNib
{
    [self setup];
}

- (instancetype)initWithFrame:(CGRect)frame
{
    if (self = [super initWithFrame:frame]) {
        [self setup];
    }
    return self;
}

- (void)setup
{
    self.userInteractionEnabled = YES;
    [self addGestureRecognizer:[[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(labelClick)]];
}

//====================================================================
/**
 * 让label有资格成为第一响应者
 */
- (BOOL)canBecomeFirstResponder
{
    return YES;
}

/**
 * label能执行哪些操作(比如copy, paste等等)
 * @return  YES:支持这种操作
 */
- (BOOL)canPerformAction:(SEL)action withSender:(id)sender
{
    if (action == @selector(cut:) || action == @selector(copy:) || action == @selector(paste:)) return YES;

    return NO;
}

// ======================================================

- (void)labelClick
{
    // 1.label要成为第一响应者(作用是:告诉UIMenuController支持哪些操作, 这些操作如何处理)
    [self becomeFirstResponder];

    // 2.显示MenuController
    UIMenuController *menu = [UIMenuController sharedMenuController];
    // targetRect: MenuController需要指向的矩形框
    // targetView: targetRect会以targetView的左上角为坐标原点
    [menu setTargetRect:self.bounds inView:self];
//    [menu setTargetRect:self.frame inView:self.superview];
    [menu setMenuVisible:!menu.isMenuVisible animated:YES];
}

//===================================================================

- (void)cut:(UIMenuController *)menu
{
    // 将自己的文字复制到粘贴板
    [self copy:menu];

    // 清空文字
    self.text = nil;
}

- (void)copy:(UIMenuController *)menu
{
    // 将自己的文字复制到粘贴板
    UIPasteboard *board = [UIPasteboard generalPasteboard];
    board.string = self.text;
}

- (void)paste:(UIMenuController *)menu
{
    // 将粘贴板的文字 复制 到自己身上
    UIPasteboard *board = [UIPasteboard generalPasteboard];
    self.text = board.string;
}

@end


```


### 自定义menuItem
- 1, 在控件内部实现可以成为响应者的方法

```objc
/**
 * 让label有资格成为第一响应者
 */
- (BOOL)canBecomeFirstResponder
{
    return YES;
}

/**
 * label能执行哪些操作(比如copy, paste等等)
 * @return  YES:支持这种操作
 */
- (BOOL)canPerformAction:(SEL)action withSender:(id)sender
{
    return NO;
}
```
- 2, 在外部为控件添加点击手势

```objc
    self.label.userInteractionEnabled = YES;
    [self.label addGestureRecognizer:[[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(labelClick)]];

```

- 3, 在点击的时候拿到menuVc设置自定义的按钮

```objc

- (void)labelClick
{
    // 1.label要成为第一响应者(作用是:告诉UIMenuController支持哪些操作, 这些操作如何处理)
    [self.label becomeFirstResponder];

    // 2.显示MenuController
    UIMenuController *menu = [UIMenuController sharedMenuController];

    // 添加MenuItem
    UIMenuItem *ding = [[UIMenuItem alloc] initWithTitle:@"顶" action:@selector(ding:)];
    UIMenuItem *replay = [[UIMenuItem alloc] initWithTitle:@"回复" action:@selector(replay:)];
    UIMenuItem *report = [[UIMenuItem alloc] initWithTitle:@"举报" action:@selector(report:)];
    menu.menuItems = @[ding, replay, report];

    [menu setTargetRect:self.label.bounds inView:self.label];
    [menu setMenuVisible:!menu.isMenuVisible animated:YES];
}
```

- 4, 必须实现item中的@selector(ding:), 否则item会不显示!!!!

```objc
- (void)ding:(UIMenuController *)menu
{
    NSLog(@"%s %@", __func__ , menu);
}

- (void)replay:(UIMenuController *)menu
{
    NSLog(@"%s %@", __func__ , menu);
}

- (void)report:(UIMenuController *)menu
{
    NSLog(@"%s %@", __func__ , menu);
}

```


## UIMenuController的示例
![](./LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20150803_3379.png)

## UIMenuController须知
- 默认情况下, 有以下控件已经支持UIMenuController
    - UITextField
    - UITextView
    - UIWebView

## 让其他控件也支持UIMenuController(比如UILabel)
- 自定义UILabel
- 重写2个方法

```objc
/**
 * 让label有资格成为第一响应者
 */
- (BOOL)canBecomeFirstResponder
{
    return YES;
}

/**
 * label能执行哪些操作(比如copy, paste等等)
 * @return  YES:支持这种操作
 */
- (BOOL)canPerformAction:(SEL)action withSender:(id)sender
{
    if (action == @selector(cut:) || action == @selector(copy:) || action == @selector(paste:)) return YES;

    return NO;
}
```

- 实现各种操作方法

```objc
- (void)cut:(UIMenuController *)menu
{
    // 将自己的文字复制到粘贴板
    [self copy:menu];

    // 清空文字
    self.text = nil;
}

- (void)copy:(UIMenuController *)menu
{
    // 将自己的文字复制到粘贴板
    UIPasteboard *board = [UIPasteboard generalPasteboard];
    board.string = self.text;
}

- (void)paste:(UIMenuController *)menu
{
    // 将粘贴板的文字 复制 到自己身上
    UIPasteboard *board = [UIPasteboard generalPasteboard];
    self.text = board.string;
}
```
- 让label成为第一响应者

```objc
// 这里的self是label
[self becomeFirstResponder];
```

- 显示UIMenuController

```objc
UIMenuController *menu = [UIMenuController sharedMenuController];
// targetRect: MenuController需要指向的矩形框
// targetView: targetRect会以targetView的左上角为坐标原点
[menu setTargetRect:self.bounds inView:self];
// [menu setTargetRect:self.frame inView:self.superview];
[menu setMenuVisible:YES animated:YES];
```

## 自定义UIMenuController内部的Item
- 添加item

```objc
// 添加MenuItem(点击item, 默认会调用控制器的方法)
UIMenuItem *ding = [[UIMenuItem alloc] initWithTitle:@"顶" action:@selector(ding:)];
UIMenuItem *replay = [[UIMenuItem alloc] initWithTitle:@"回复" action:@selector(replay:)];
UIMenuItem *report = [[UIMenuItem alloc] initWithTitle:@"举报" action:@selector(report:)];
menu.menuItems = @[ding, replay, report];
```

