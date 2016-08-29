# 解压缩

![](file:///Users/apple/Desktop/Library/LibrarypPictures/RunNet/05-文件上传下载/Snip20160705_2.png)

```objc

- (void)unarchive
{
    [SSZipArchive unzipFileAtPath:@"/Users/apple/Desktop/0705-0720/大文件的下载-4/test22.zip"
    toDestination:@"/Users/apple/Desktop/0705-0720/大文件的写入-2"];

}
- (void)createZipdir
{
    [SSZipArchive createZipFileAtPath:
    @"/Users/apple/Desktop/0705-0720/大文件的下载-4/test22.zip" withContentsOfDirectory:@"/Users/apple/Desktop/0705-0720/AFNetworking"];

}

- (void)createZipFile
{
    NSArray *array = @[
                       @"/Users/apple/Desktop/LMJ_Macroes.h",
                       @"/Users/apple/Desktop/path.m",
                       @"/Users/apple/Desktop/path.txt",
                       @"/Users/apple/Desktop/2015年IOS大神班课表.xls"

                       ];

    [SSZipArchive createZipFileAtPath:
    @"/Users/apple/Desktop/0705-0720/大文件的下载-4/test.zip" withFilesAtPaths:array];

}

```
