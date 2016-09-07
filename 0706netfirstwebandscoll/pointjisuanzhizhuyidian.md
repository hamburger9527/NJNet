
## 指针p的加减法运算
- 指针p + N
    - p里面存储的地址值 + N * 指针所指向类型的占用字节数
- 指针p - N
    - p里面存储的地址值 - N * 指针所指向类型的占用字节数

## 数组名
- 存储的是`数组首元素`的地址
- 等价于:一个指向`数组首元素`的指针
- `数组名 + 1` 的跨度：`数组首元素`的占用字节数

## 其他结论
- `&num + 1`的跨度：`num`的占用字节数


### demo1

```objc
        int numbers[2][2][2] = {
            {
                {10, 20},
                {30, 40},
            },
            {
                {50, 60},
                {70, 80}
            }
        };

        NSLog(@"%p, %p, %p, %p", numbers, &numbers, numbers + 1, &numbers + 1);

        /*
          0x7fff5fbff760, 0x7fff5fbff760, 0x7fff5fbff770, 0x7fff5fbff780
         */

```


### demo-2

```obj

        int numbers[4] = {10, 20, 30, 40};

        int *p = (int *)(&numbers + 1);

        NSLog(@"%d", *(p - 1));  // 结果40

```


### demo

```objc
        int numbers[2][2] = {
            {10, 20}, // numbers[0]
            {11, 22} // numbers[1]
        };

        NSLog(@"%p %p", &numbers, &numbers + 1);

        int numbers[2][2][2] = {
            {
                {10, 20},
                {30, 40},
            },
            {
                {50, 60},
                {70, 80}
            }
        };

         numbers[0][0] == &numbers[0][0][0],相当于是一个指向numbers[0][0][0]的指针
         numbers[1] == &numbers[1][0],相当于是一个指向numbers[1][0]的指针
         numbers == &numbers[0],相当于是一个指向numbers[0]的指针
         &numbers == 相当于是一个指向numbers的指针
        NSLog(@"%p %p", &numbers, &numbers + 1);

```
