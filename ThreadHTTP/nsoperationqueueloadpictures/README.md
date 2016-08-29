# 0709NSOperationQueue/多图片下载


- 没有GCD里边的`once`,`after`, `apply`


- `NSInvocationOperation` and `NSBlockOperation` 的基本使用

```objc
- (void)blockOperation
{
    NSBlockOperation *op = [NSBlockOperation blockOperationWithBlock:^{
        // 在主线程
        NSLog(@"下载1------%@", [NSThread currentThread]);
    }];

    // 添加额外的任务(在子线程执行), 并发队列
    [op addExecutionBlock:^{
        NSLog(@"下载2------%@", [NSThread currentThread]);
    }];

    [op addExecutionBlock:^{
        NSLog(@"下载3------%@", [NSThread currentThread]);
    }];
    [op addExecutionBlock:^{
        NSLog(@"下载4------%@", [NSThread currentThread]);
    }];

    [op start];
}

- (void)invocationOperation
{
    NSInvocationOperation *op = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(run) object:nil];

    // 默认在主线程串行执行
    [op start];
}

- (void)run
{
    NSLog(@"------%@", [NSThread currentThread]);
}

```

- `operationqueue`-1

```objc

- (void)operationQueue1
{
    // 创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];

    // 创建操作（任务）
    // 创建NSInvocationOperation
    NSInvocationOperation *op1 = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(download1) object:nil];
    NSInvocationOperation *op2 = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(download2) object:nil];

    // 创建NSBlockOperation
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"download3 --- %@", [NSThread currentThread]);
    }];

    [op3 addExecutionBlock:^{
        NSLog(@"download4 --- %@", [NSThread currentThread]);
    }];
    [op3 addExecutionBlock:^{
        NSLog(@"download5 --- %@", [NSThread currentThread]);
    }];


    NSBlockOperation *op4 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"download6 --- %@", [NSThread currentThread]);
    }];

    // 创建XMGOperation
    XMGOperation *op5 = [[XMGOperation alloc] init];

    // 添加任务到队列中
    [queue addOperation:op1]; // [op1 start]
    [queue addOperation:op2]; // [op2 start]
    [queue addOperation:op3]; // [op3 start]
    [queue addOperation:op4]; // [op4 start]
    [queue addOperation:op5]; // [op5 start]
}


```


- `queue addOperationWithBlock:`-2

```objc

- (void)operationQueue2
{
    // 创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];

    // 创建操作
    //    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
    //        NSLog(@"download1 --- %@", [NSThread currentThread]);
    //    }];

    // 添加操作到队列中
    //    [queue addOperation:op1];
    [queue addOperationWithBlock:^{
        NSLog(@"download1 --- %@", [NSThread currentThread]);
    }];
    [queue addOperationWithBlock:^{
        NSLog(@"download2 --- %@", [NSThread currentThread]);
    }];
}

```

- `maxConcurrentOperationCount`-3

```objc

- (void)opetationQueue3
{
    // 创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];

    // 设置最大并发操作数
    //    queue.maxConcurrentOperationCount = 2;
    queue.maxConcurrentOperationCount = 1; // 就变成了串行队列

    // 添加操作
    [queue addOperationWithBlock:^{
        NSLog(@"download1 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];
    [queue addOperationWithBlock:^{
        NSLog(@"download2 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];
    [queue addOperationWithBlock:^{
        NSLog(@"download3 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];
    [queue addOperationWithBlock:^{
        NSLog(@"download4 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];
    [queue addOperationWithBlock:^{
        NSLog(@"download5 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];
    [queue addOperationWithBlock:^{
        NSLog(@"download6 --- %@", [NSThread currentThread]);
        [NSThread sleepForTimeInterval:0.01];
    }];
}

```


- `suspended`-4

```objc
    if (self.queue.isSuspended) {
        // 恢复队列，继续执行
        self.queue.suspended = NO;
    } else {
        // 暂停（挂起）队列，暂停执行
        self.queue.suspended = YES;
    }


```

- `cancelOperation`

```objc

 [self.queue cancelAllOperations];

 if (self.isCancelled) return;

```

- NSOperation-`dependency`

```objc

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];

    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"download1----%@", [NSThread  currentThread]);
    }];
    NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"download2----%@", [NSThread  currentThread]);
    }];
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"download3----%@", [NSThread  currentThread]);
    }];
    NSBlockOperation *op4 = [NSBlockOperation blockOperationWithBlock:^{
        for (NSInteger i = 0; i<10; i++) {
            NSLog(@"download4----%@", [NSThread  currentThread]);
        }
    }];
    NSBlockOperation *op5 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"download5----%@", [NSThread  currentThread]);
    }];

//    op5.completionBlock = ^{
//        NSLog(@"op5执行完毕---%@", [NSThread currentThread]);
//    };

    // 设置依赖
    [op3 addDependency:op1];
    [op3 addDependency:op2];
    [op3 addDependency:op4];

    [queue addOperation:op1];
    [queue addOperation:op2];
    [queue addOperation:op3];
    [queue addOperation:op4];
    [queue addOperation:op5];
}

@end

```

- 线程之间的通信

```objc

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];

    __block UIImage *image1 = nil;
    // 下载图片1
    NSBlockOperation *download1 = [NSBlockOperation blockOperationWithBlock:^{

        // 图片的网络路径
        NSURL *url = [NSURL URLWithString:@"http://img.pconline.com.cn/images/photoblog/9/9/8/1/9981681/200910/11/1255259355826.jpg"];

        // 加载图片
        NSData *data = [NSData dataWithContentsOfURL:url];

        // 生成图片
        image1 = [UIImage imageWithData:data];
    }];

    __block UIImage *image2 = nil;
    // 下载图片2
    NSBlockOperation *download2 = [NSBlockOperation blockOperationWithBlock:^{

        // 图片的网络路径
        NSURL *url = [NSURL URLWithString:@"http://pic38.nipic.com/20140228/5571398_215900721128_2.jpg"];


        // 加载图片
        NSData *data = [NSData dataWithContentsOfURL:url];

        // 生成图片
        image2 = [UIImage imageWithData:data];
    }];

    // 合成图片
    NSBlockOperation *combine = [NSBlockOperation blockOperationWithBlock:^{
        // 开启新的图形上下文
        UIGraphicsBeginImageContext(CGSizeMake(100, 100));

        // 绘制图片
        [image1 drawInRect:CGRectMake(0, 0, 50, 100)];
        image1 = nil;

        [image2 drawInRect:CGRectMake(50, 0, 50, 100)];
        image2 = nil;

        // 取得上下文中的图片
        UIImage *image = UIGraphicsGetImageFromCurrentImageContext();

        // 结束上下文
        UIGraphicsEndImageContext();

        // 回到主线程
        [[NSOperationQueue mainQueue] addOperationWithBlock:^{
            self.imageView.image = image;
        }];
    }];
    [combine addDependency:download1];
    [combine addDependency:download2];

    [queue addOperation:download1];
    [queue addOperation:download2];
    [queue addOperation:combine];
}

```

- 程序联网的配置plist

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160701_5.png)

- 代码plish-source配置联网状态

```objc
//访问网络
<key>NSAppTransportSecurity</key>
<dict>
<key>NSAllowsArbitraryLoads</key>
<true/>
</dict>

```

