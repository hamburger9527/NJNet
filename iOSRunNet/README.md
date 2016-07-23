
![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/Snip20160626_11.png)

## 本流程学完后的补缺

`- 0, svn 和 git 本流程完成`-已完成

- 1, 百思不得姐里边的 `dynamic`物理动画
- 2, 瀑布流- 也在百思不得姐里边有
- 3, UI补充里边的真机相关调试


## 常用的宏


```objc

#define LMJKeyPath(obj, key) @(((void)obj.key, #key))

    LMJKeyPath(p, name); @"name"

#define LMJAngle2Radion(angle) ((angle) / 180.0 * M_PI)

#define LMJFuncLog NSLog(@"%s", __func__)

#define LMJLog(...) NSLog(@"%@", [NSString stringWithUTF8String:#__VA_ARGS__])




#ifdef __OBJC__


#define LMJFuncLog NSLog(@"%s", __func__)

#define LMJLog(...) NSLog(@"%@", [NSString stringWithUTF8String:#__VA_ARGS__])


#endif

<key>NSAppTransportSecurity</key>
<dict>
<key>NSAllowsArbitraryLoads</key>
<true/>
</dict>

sudo apachectl -k restart

```
