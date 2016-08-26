# publishVc 和 publishView


-  pop和Core Animation的区别
    - 1.Core Animation的动画只能添加到layer上
    - 2.pop的动画能添加到任何对象
    - 3.pop的底层并非基于Core Animation, 是基于CADisplayLink
    - 4.Core Animation的动画仅仅是表象, 并不会真正修改对象的frame\size等值
    - 5.pop的动画实时修改对象的属性, 真正地修改了对象的属性

- 弹簧动画`POPSpringAnimation`
    - 里边用到的开始时间一定要用**CACurrentMediaTime()**
        - 设置延时**anim.beginTime = i  LMJAnimationDelay + CACurrentMediaTime();**
    - 里边用到的**animationWithPropertyNamed:kPOPViewFrame**,全部用K开头
    - 添加动画**[btn pop_addAnimation:anim forKey:nil]**

```objc
        // 添加动画
        POPSpringAnimation *anim = [POPSpringAnimation animationWithPropertyNamed:kPOPViewFrame];
        // 设置起始开始的位置
        anim.fromValue = [NSValue valueWithCGRect:CGRectMake(btnX, btnY - LMJMainScreenHeight, btnWidth, btnHeight)];
        anim.toValue = [NSValue valueWithCGRect:CGRectMake(btnX, btnY, btnWidth, btnHeight)];

        // 设置弹性系数
        anim.springBounciness = LMJSpringBounciness;
        anim.springSpeed = LMJSpringSpeed;

        // 设置延时
        anim.beginTime = i * LMJAnimationDelay + CACurrentMediaTime();


        // 添加到btn上边
        [btn pop_addAnimation:anim forKey:nil];

```

- 普通的动画`POPBasicAnimation`

```objc

        UIButton *btn = self.btns[index];

        // 添加动画
        POPBasicAnimation *anim = [POPBasicAnimation animationWithPropertyNamed:kPOPViewCenter];

        // 动画的执行节奏(一开始很慢, 后面很快)
        //        anim.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseIn];

        // 添加路径
//       anim.fromValue = [NSValue valueWithCGPoint:btn.center];
        anim.duration = 0.2;

        anim.toValue = [NSValue valueWithCGPoint:CGPointMake(btn.centerX,LMJMainScreenHeight + btn.height)];


        // 设置延时
        anim.beginTime = CACurrentMediaTime() + LMJAnimationDelay * index;

//        LMJLog(@"%@", [NSThread currentThread]);
        // 添加动画
        [btn pop_addAnimation:anim forKey:nil];

```

- 在动画开始过程中要禁止用户和view的交互, 动画完成后才让和用户交互


### 或者直接用xib来添加到窗口上, 窗口很NB, 直接设置**hidden = NO**就直接显示了

```objc

- (void)didMoveToSuperview
{
    [super didMoveToSuperview];
    self.frame = self.superview.bounds;
}

// 必须要保住窗口的命
static UIWindow *window_ = nil;
//    LMJKeyWindow.userInteractionEnabled = NO;

+ (void)show
{
    window_ = [[UIWindow alloc] initWithFrame:LMJMainScreen.bounds];

    [window_ addSubview:[LMJPublishView publishView]];

    window_.hidden = NO;
}

```






