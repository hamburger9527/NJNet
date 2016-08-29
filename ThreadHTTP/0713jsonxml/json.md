# JSON

## JSON数据（NSData） -> OC对象（Foundation Object）

```OBJC
- {} -> NSDictionary @{}
- [] -> NSArray @[]
- "jack" -> NSString @"jack"
- 10 -> NSNumber @10
- 10.5 -> NSNumber @10.5
- true -> NSNumber @1
- false -> NSNumber @0
- null -> NSNull

```

## JSON数据（NSData） -> OC对象（Foundation Object）
```objc
// 利用NSJSONSerialization类
+ (id)JSONObjectWithData:(NSData *)data options:(NSJSONReadingOptions)opt error:(NSError **)error;
```
- NSJSONReadingOptions
    - NSJSONReadingMutableContainers = (1UL << 0)
        - 创建出来的数组和字典就是可变
    - NSJSONReadingMutableLeaves = (1UL << 1)
        - 数组或者字典里面的字符串是可变的
    - NSJSONReadingAllowFragments
        - 允许解析出来的对象不是字典或者数组，比如直接是字符串或者NSNumber

## OC对象（Foundation Object）-> JSON数据（NSData）
```objc
// 利用NSJSONSerialization类
+ (NSData *)dataWithJSONObject:(id)obj options:(NSJSONWritingOptions)opt error:(NSError **)error;
```

## 格式化服务器返回的JSON数据
- 在线格式化：http://tool.oschina.net/codeformat/json
- 将服务器返回的字典或者数组写成plist文件
