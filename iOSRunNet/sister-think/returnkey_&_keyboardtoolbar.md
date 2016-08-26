# returnKey & keyboardToolBar

#### pragma mark - <UITextFieldDelegate>

```objc

/**
 * 当点击键盘右下角的return key时,就会调用这个方法
 */

- (BOOL)textFieldShouldReturn:(UITextField *)textField
{
    if(textField == self.nameField)
    {
        [self.emailField becomeFirstResponder];
    }
    else if (textField == self.emailField)
    {
        [self.addressField becomeFirstResponder];
    }
    else if(textField == self.addressField)
    {
        [self.view endEditing:YES];
    }

    return YES;
}

/**
 *  当开始编辑后, 要联动toolbar, 判断toolbar里边的按钮能不能点击
 */
- (void)textFieldDidBeginEditing:(UITextField *)textField
{
    self.currentIndex = [self.keyboards indexOfObject:textField];
}

```

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/0722-0807百思不得姐/Snip20160731_19.png)


###toolBar `.h`

```objc

typedef enum : NSUInteger {

    LMJKeyboardToolBarItemPrevious,
    LMJKeyboardToolBarItemNext,
    LMJKeyboardToolBarItemDone,

} LMJKeyboardToolBarItem;

@class LMJKeyboardToolBar;

@protocol LMJKeyboardToolBarDelegate <NSObject>

@optional
/**
 *  通知外界发生了点击事件
 *
 *  @param keyboardToolBar 自己
 *  @param item            通知外界点击了哪个按钮
 */
- (void)keyboardToolBar:(LMJKeyboardToolBar *)keyboardToolBar didClickItem:(LMJKeyboardToolBarItem)item;

@end

@interface LMJKeyboardToolBar : UIToolbar

/** 代理监听 */
@property (weak, nonatomic) id<LMJKeyboardToolBarDelegate> keyboardToolBarDelegate;

/**
 *  快速创建对象
 */
+ (instancetype)keyboardToolBar;


/**
 *  设置里边上次个下次的按钮能否被点击
 */
- (void)keyboardToolBarItemPreviousButtonEnable:(BOOL)isClick;

/**
 *  设置里边上次个下次的按钮能否被点击
 */
- (void)keyboardToolBarItemNextButtonEnable:(BOOL)isClick;

@end


```

### toolbar的`.m`

```objc
#import "LMJKeyboardToolBar.h"

@interface LMJKeyboardToolBar ()
@property (weak, nonatomic) IBOutlet UIBarButtonItem *previousItem;
@property (weak, nonatomic) IBOutlet UIBarButtonItem *nextItem;
@property (weak, nonatomic) IBOutlet UIBarButtonItem *doneItem;

/** <#digest#> */
@property (nonatomic, copy) void(^canClick)();

@end

@implementation LMJKeyboardToolBar

- (IBAction)previous:(id)sender {

    if([self.keyboardToolBarDelegate respondsToSelector:@selector(keyboardToolBar:didClickItem:)])
    {
        [self.keyboardToolBarDelegate keyboardToolBar:self didClickItem:LMJKeyboardToolBarItemPrevious];
    }

}
- (IBAction)next:(id)sender {


    if([self.keyboardToolBarDelegate respondsToSelector:@selector(keyboardToolBar:didClickItem:)])
    {
        [self.keyboardToolBarDelegate keyboardToolBar:self didClickItem:LMJKeyboardToolBarItemNext];
    }

}
- (IBAction)done:(id)sender {

    if([self.keyboardToolBarDelegate respondsToSelector:@selector(keyboardToolBar:didClickItem:)])
    {
        [self.keyboardToolBarDelegate keyboardToolBar:self didClickItem:LMJKeyboardToolBarItemDone];
    }
}

- (void)keyboardToolBarItemNextButtonEnable:(BOOL)isClick
{
    self.nextItem.enabled = isClick;
}

- (void)keyboardToolBarItemPreviousButtonEnable:(BOOL)isClick
{
    self.previousItem.enabled = isClick;
}

+ (instancetype)keyboardToolBar
{
    LMJKeyboardToolBar *toolBar = [[NSBundle mainBundle] loadNibNamed:NSStringFromClass(self) owner:nil options:nil].lastObject;

    return toolBar;
}


@end

```


##vc里边怎么用

```objc

#import "ViewController.h"
#import "LMJKeyboardToolBar.h"

@interface ViewController ()<UITextFieldDelegate, LMJKeyboardToolBarDelegate>
@property (weak, nonatomic) IBOutlet UITextField *nameField;
@property (weak, nonatomic) IBOutlet UITextField *emailField;
@property (weak, nonatomic) IBOutlet UITextField *addressField;
/** <#digest#> */
@property (weak, nonatomic) LMJKeyboardToolBar *tooBar;

/** <#digest#> */
@property (nonatomic, strong) NSArray *keyboards;

/** <#digest#> */
@property (assign, nonatomic) NSInteger currentIndex;

@end

@implementation ViewController

- (NSArray *)keyboards
{
    if(_keyboards == nil)
    {
        _keyboards = @[self.nameField, self.emailField, self.addressField];
    }
    return _keyboards;
}

- (void)viewDidLoad {
    [super viewDidLoad];

    LMJKeyboardToolBar *toolBar = [LMJKeyboardToolBar keyboardToolBar];

    toolBar.keyboardToolBarDelegate = self;
    self.nameField.inputAccessoryView = toolBar;
    self.emailField.inputAccessoryView = toolBar;
    self.addressField.inputAccessoryView = toolBar;

    self.nameField.delegate = self;
    self.emailField.delegate = self;
    self.addressField.delegate = self;

    self.tooBar = toolBar;

}

- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    [self.view endEditing:YES];
}

#pragma mark - <UITextFieldDelegate>
/**
 * 当点击键盘右下角的return key时,就会调用这个方法
 */

- (BOOL)textFieldShouldReturn:(UITextField *)textField
{
    if(textField == self.nameField)
    {
        [self.emailField becomeFirstResponder];

    }
    else if (textField == self.emailField)
    {
        [self.addressField becomeFirstResponder];
    }
    else if(textField == self.addressField)
    {
        [self.view endEditing:YES];
    }

    return YES;
}

/**
 *  当开始编辑后, 要联动toolbar, 判断toolbar里边的按钮能不能点击
 */
- (void)textFieldDidBeginEditing:(UITextField *)textField
{
    self.currentIndex = [self.keyboards indexOfObject:textField];
}


#pragma mark - <LMJKeyboardToolBarDelegate>

- (void)keyboardToolBar:(LMJKeyboardToolBar *)keyboardToolBar didClickItem:(LMJKeyboardToolBarItem)item
{
    if(item == LMJKeyboardToolBarItemPrevious)
    {
        for (UIView *view in self.view.subviews) {

            if([view isFirstResponder])
            {
                NSInteger index = [self.keyboards indexOfObject:view];

                index--;

                [self.keyboards[index] becomeFirstResponder];
                self.currentIndex = index;

                break;
            }
        }
    }
    if(item == LMJKeyboardToolBarItemNext)
    {
        for (UIView *view in self.view.subviews) {

            if([view isFirstResponder])
            {
                NSInteger index = [self.keyboards indexOfObject:view];

                index++;

                [self.keyboards[index] becomeFirstResponder];
                self.currentIndex = index;
                break;
            }
        }
    }

    if(item == LMJKeyboardToolBarItemDone)
    {
        [self.view endEditing:YES];
    }
}

// 重写setter方法监听, currentindex的变化
- (void)setCurrentIndex:(NSInteger)currentIndex
{
    _currentIndex = currentIndex;
    // 当索引数字等于最后一个textfiled的时候, toolbar里边下一个按钮不饿能够点击
    [self.tooBar keyboardToolBarItemNextButtonEnable:(currentIndex != (self.keyboards.count-1))];

    // toolbar礼拜呢的上一个按钮, 不能被点击, 因为, 索引是0的时候
    [self.tooBar keyboardToolBarItemPreviousButtonEnable:(currentIndex != 0)];

}

@end


```
