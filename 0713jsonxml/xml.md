# XML

## XML的解析方式
- SAX
    - 大小文件都可以
    - NSXMLParser
- DOM
    - 最好是小文件
    - GDataXML

## NSXMLParser的用法
- 创建解析器来解析

```objc
// 创建XML解析器
NSXMLParser *parser = [[NSXMLParser alloc] initWithData:data];

// 设置代理
parser.delegate = self;

// 开始解析XML(parse方法是阻塞式的)
[parser parse];
```

- 代理对象要遵守NSXMLParserDelegate协议，实现代理方法

```objc
/**
 * 解析到某个元素的结尾（比如解析</videos>）
 */
- (void)parser:(NSXMLParser *)parser didEndElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
{

}

/**
 * 解析到某个元素的开头（比如解析<videos>）
 */
- (void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName attributes:(NSDictionary *)attributeDict
{
    if ([elementName isEqualToString:@"videos"]) return;
//    XMGVideo *video = [[XMGVideo alloc] init];
//    video.keyValues = attributeDict;

    XMGVideo *video = [XMGVideo objectWithKeyValues:attributeDict];
    [self.videos addObject:video];
}

/**
 * 开始解析XML文档
 */
- (void)parserDidStartDocument:(NSXMLParser *)parser
{

}

/**
 * 解析完毕
 */
- (void)parserDidEndDocument:(NSXMLParser *)parser
{

}
```

## GDataXML
- 配置
![](../LibrarypPictures/RunNet/04-JSON和XML/Snip20150713_336.png)
![](../LibrarypPictures/RunNet/04-JSON和XML/Snip20150713_340.png)

- 设置非ARC标记
![](../LibrarypPictures/RunNet/04-JSON和XML/Snip20150713_335.png)

- 具体用法

```objc
// 加载整个文档
GDataXMLDocument *doc = [[GDataXMLDocument alloc] initWithData:data options:0 error:nil];

// 获得根节点
doc.rootElement;

// 获得其他节点
[element elementsForName:@"video"];

// 获得节点的属性
[element attributeForName:@"name"].stringValue;


        // 加载整个文档
        GDataXMLDocument *doc = [[GDataXMLDocument alloc] initWithData:data options:0 error:nil];

        // 获得所有video元素
        NSArray *elements = [doc.rootElement elementsForName:@"video"];


        for (GDataXMLElement *ele in elements) {..
            XMGVideo *video = [[XMGVideo alloc] init];
            video.name = [ele attributeForName:@"name"].stringValue;
            video.url = [ele attributeForName:@"url"].stringValue;
            video.image = [ele attributeForName:@"image"].stringValue;
            video.length = [ele attributeForName:@"length"].stringValue.integerValue;

            [self.videos addObject:video];
        }

```
