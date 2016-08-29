# 0708GCD/单例模式

- NSThread 的简单使用

```objc

[NSThread currentThread]


 NSThread *thread = [[NSThread alloc] initWithTarget:self selector:@selector(run:) object:@"jack"];
    thread.name = @"rose";
    [thread start];

[NSThread detachNewThreadSelector:@selector(run:) toTarget:self withObject:@"rose"];


[self performSelectorInBackground:@selector(run:) withObject:@"jack"];

[NSThread exit];

[NSThread sleepForTimeInterval:2];

[NSThread sleepUntilDate:[NSDate distantFuture]];

[NSThread sleepUntilDate:[NSDate dateWithTimeIntervalSinceNow:2]];

[NSThread currentThread].name


// 回到主线程，显示图片
[self.imageView performSelector:@selector(setImage:) onThread:[NSThread mainThread] withObject:image waitUntilDone:NO];
[self.imageView performSelectorOnMainThread:@selector(setImage:) withObject:image waitUntilDone:NO];
[self performSelectorOnMainThread:@selector(showImage:) withObject:image waitUntilDone:YES];
[self performSelector:@selector(run) withObject:nil afterDelay:2.0]

[NSThread mainThread]

[NSThread isMainThread]

CFTimeInterval begin = CFAbsoluteTimeGetCurrent();


```

- dispatch_queue_t

```objc
    // 并发队列
    dispatch_queue_t queue0 = dispatch_queue_create("adfs", DISPATCH_QUEUE_CONCURRENT);

    // 串行队列
    dispatch_queue_t queue1 = dispatch_queue_create("adfs", DISPATCH_QUEUE_SERIAL);

    // 全局并发队列
    dispatch_queue_t queue2 = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

    // 主队列
    dispatch_queue_t queue3 = dispatch_get_main_queue();

dispatch_async(queue, ^{
        for (NSInteger i = 0; i<10; i++) {
            NSLog(@"3-----%@", [NSThread currentThread]);
        }
    });
dispatch_sync(queue, ^(){
        for (NSInteger i = 0; i<10; i++) {
            NSLog(@"3-----%@", [NSThread currentThread]);
        }
});

```


- GCD通信

```objc
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        // 图片的网络路径
        NSURL *url = [NSURL URLWithString:@"http://img.pconline.com.cn/images/photoblog/9/9/8/1/9981681/200910/11/1255259355826.jpg"];

        // 加载图片
        NSData *data = [NSData dataWithContentsOfURL:url];

        // 生成图片
        UIImage *image = [UIImage imageWithData:data];

        // 回到主线程
        dispatch_async(dispatch_get_main_queue(), ^{
            self.imageView.image = image;
        });
    });

```

- dispatch_barrier_async

```objc
    dispatch_barrier_async(queue, ^(){

        NSLog(@"----barrier-----%@", [NSThread currentThread]);
    });

```

- delay

```objc

- (void)delay
{
    [self performSelector:@selector(run) withObject:nil afterDelay:2];

    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)) , queue, ^(){

        [self run];

    });

    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^(){

        [self run];
    });

    [NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(run) userInfo:nil repeats:NO];
}

```

- once

```objc
- (void)once
{
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^(){


    });
}

```

- 快速迭代

```objc

/**
 * 传统文件剪切
 */
- (void)moveFile
{
    NSString *from = @"/Users/xiaomage/Desktop/From";
    NSString *to = @"/Users/xiaomage/Desktop/To";

    NSFileManager *mgr = [NSFileManager defaultManager];
    NSArray *subpaths = [mgr subpathsAtPath:from];

    for (NSString *subpath in subpaths) {

        NSString *fromFullpath = [from stringByAppendingPathComponent:subpath];
        NSString *toFullpath = [to stringByAppendingPathComponent:subpath];


        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
            // 剪切
            [mgr moveItemAtPath:fromFullpath toPath:toFullpath error:nil];
        });
    }
}




/**
 * 快速迭代
 */
- (void)apply
{
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);



    NSString *from = @"/Users/xiaomage/Desktop/From";
    NSString *to = @"/Users/xiaomage/Desktop/To";

    NSFileManager *mgr = [NSFileManager defaultManager];
    NSArray *subpaths = [mgr subpathsAtPath:from];

    dispatch_apply(subpaths.count, queue, ^(size_t index) {



        NSString *subpath = subpaths[index];


        NSString *fromFullpath = [from stringByAppendingPathComponent:subpath];
        NSString *toFullpath = [to stringByAppendingPathComponent:subpath];
        // 剪切
        [mgr moveItemAtPath:fromFullpath toPath:toFullpath error:nil];

        NSLog(@"%@---%@", [NSThread currentThread], subpath);
    });
}



```

- group队列组

```objc

- (void)group
{

    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    // 创建一个队列组
    dispatch_group_t group = dispatch_group_create();

    // 1.下载图片1
    dispatch_group_async(group, queue, ^{
        // 图片的网络路径
        NSURL *url = [NSURL URLWithString:@"http://img.pconline.com.cn/images/photoblog/9/9/8/1/9981681/200910/11/1255259355826.jpg"];

        // 加载图片
        NSData *data = [NSData dataWithContentsOfURL:url];

        // 生成图片
        self.image1 = [UIImage imageWithData:data];
    });

    // 2.下载图片2
    dispatch_group_async(group, queue, ^{
        // 图片的网络路径
        NSURL *url = [NSURL URLWithString:@"http://pic38.nipic.com/20140228/5571398_215900721128_2.jpg"];

        // 加载图片
        NSData *data = [NSData dataWithContentsOfURL:url];

        // 生成图片
        self.image2 = [UIImage imageWithData:data];
    });

    // 3.将图片1、图片2合成一张新的图片
    dispatch_group_notify(group, queue, ^{
        // 开启新的图形上下文
        UIGraphicsBeginImageContext(CGSizeMake(100, 100));

        // 绘制图片
        [self.image1 drawInRect:CGRectMake(0, 0, 50, 100)];
        [self.image2 drawInRect:CGRectMake(50, 0, 50, 100)];

        // 取得上下文中的图片
        UIImage *image = UIGraphicsGetImageFromCurrentImageContext();

        // 结束上下文
        UIGraphicsEndImageContext();

        // 回到主线程显示图片
        dispatch_async(dispatch_get_main_queue(), ^{
            // 4.将新图片显示出来
            self.imageView.image = image;
        });
    });
}

```

















































