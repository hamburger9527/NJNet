## 字典转模型框架
- Mantle
    - 所有模型都必须继承自MTModel
- JSONModel
    - 所有模型都必须继承自JSONModel
- MJExtension
    - 不需要强制继承任何其他类


## 设计框架需要考虑的问题
- 侵入性
    - 侵入性大就意味着很难离开这个框架
- 易用性
    - 比如少量代码实现N多功能
- 扩展性
    - 很容易给这个框架增加新框架

```objc

        // 解析JSON
        NSDictionary *dict = [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:nil];

        // 获得视频的模型数组
        self.videos = [XMGVideo objectArrayWithKeyValuesArray:dict[@"videos"]];


    XMGVideo *video = [XMGVideo objectWithKeyValues:attributeDict];
    [self.videos addObject:video];

// 当模型的key和数据中的key不一样的时候, 模型中实现这个方法, 告诉其真实的key
+ (NSDictionary *)replacedKeyFromPropertyName
{
    return @{
             @"ID" : @"id"
             };
}

```
