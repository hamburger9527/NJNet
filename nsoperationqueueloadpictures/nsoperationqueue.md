# NSOperationQueue

## GCD的队列类型
- 并发队列
    - 自己创建的
    - 全局
- 串行队列
    - 主队列
    - 自己创建的

## NSOperationQueue的队列类型
- 主队列
    - [NSOperationQueue mainQueue]
    - 凡是添加到主队列中的任务（NSOperation），都会放到主线程中执行
- 非主队列（其他队列）
    - [[NSOperationQueue alloc] init]
    - 同时包含了：串行、并发功能
    - 添加到这种队列中的任务（NSOperation），就会自动放到子线程中执行


![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片01.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片02.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片03.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片04.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片05.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片06.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片07.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片08.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片09.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片10.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片11.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片12.jpg)

![](../LibrarypPictures/RunNet/04-NSOperation/幻灯片13.jpg)
