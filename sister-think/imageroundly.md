# imageRoundly

##第一种方式直接设置imageView的layer

```objc

//    self.imageView.layer.cornerRadius = self.imageView.width/2;
//    self.imageView.layer.masksToBounds = YES;

```

## 第二种方式用绘图`CGContextAddEllipseInRect`

```objc

- (UIImage *)circleImage
{
    // NO代表透明
    UIGraphicsBeginImageContextWithOptions(self.size, NO, 0.0);

    // 获得上下文
    CGContextRef ctx = UIGraphicsGetCurrentContext();

    // 添加一个圆
    CGRect rect = CGRectMake(0, 0, self.size.width, self.size.height);
    CGContextAddEllipseInRect(ctx, rect);

    // 裁剪
    CGContextClip(ctx);

    // 将图片画上去
    [self drawInRect:rect];

    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();

    UIGraphicsEndImageContext();

    return image;
}

```


### 第三种方式`addClip`

```objc
- (instancetype)imageClipedRoundly
{

    __block UIImage *newImage;

    dispatch_sync(dispatch_get_global_queue(0, 0), ^{

        UIGraphicsBeginImageContextWithOptions(self.size, NO, 0);

        UIBezierPath *clipPath = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(0, 0, self.size.width, self.size.height)];

        [clipPath addClip];

        [self drawAtPoint:CGPointZero];

        newImage = UIGraphicsGetImageFromCurrentImageContext();

        UIGraphicsEndImageContext();
    });

    return newImage;
}

```
