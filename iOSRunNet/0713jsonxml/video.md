# video

## 利用苹果官方API播放视频
```objc

    XMGVideo *video = self.videos[indexPath.row];
    NSString *urlStr = [@"http://120.25.226.186:32812" stringByAppendingPathComponent:video.url];

// 创建视频播放器
MPMoviePlayerViewController *vc = [[MPMoviePlayerViewController alloc] initWithContentURL:[NSURL URLWithString:urlStr]];

// 显示视频
[self presentViewController:vc animated:YES completion:nil];
```
