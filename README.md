##多线程网络,

##如有不正确的地方还望改正

### 联系 64hp@163.com

### gitbook下载地址`https://github.com/NJHu/gitbook-dmg.git`

##`关于图片加载不了的的问题`

## 由于是离线图片所以需要另外导入路径

### 把本书中根路径的`**apple**`文件夹拖入到电脑用户文件夹
![](./ThreadHTTP/images/Snip20160829_20.png)
![](./ThreadHTTP/images/Snip20160829_18.png)

---

## 本流程学完后的补缺

`- 0, svn 和 git 本流程完成`-已完成

`- 1, 百思不得姐里边的 `dynamic`物理动画`
`- 2, 瀑布流- 也在百思不得姐里边有`
- 3, UI补充里边的真机相关调试
- 4, sq-lite. 数据库的操作

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

# Summary

* [多线程和网络](README.md)
* [0705--自定义控制器的切换\级联菜单](0705-customvc_and/README.md)
   * [级联菜单](0705-customvc_and/level_table.md)
       * [导航栏和scrollView的注意点](0705-customvc_and/navgationbar.md)
   * [static](0705-customvc_and/static.md)
   * [copy](0705-customvc_and/copy.md)
* [0706--网易新闻首页\UIScrollVIew布局](0706netfirstwebandscoll/README.md)
   * [scrollView内部控件悬停](0706netfirstwebandscoll/scrollviewkongjianxuanfu.md)
   * [scrollView子控件的布局](0706netfirstwebandscoll/scrollviewdeautolayout.md)
   * [const和指针](0706netfirstwebandscoll/globalpardf.md)
   * [指针p的加减法运算](0706netfirstwebandscoll/pointjisuanzhizhuyidian.md)
* [0708GCD/单例模式](gcdandsingleton/README.md)
   * [多线程基础](gcdandsingleton/foundation.md)
   * [NSThread](gcdandsingleton/nsthread.md)
   * [GCD-Grand Central Dispatch](gcdandsingleton/grand_central_dispatch.md)
* [0709NSOperationQueue/多图片下载](nsoperationqueueloadpictures/README.md)
   * [NSOperation](nsoperationqueueloadpictures/nsoperationqueue.md)
   * [SDWebImage](nsoperationqueueloadpictures/sdwebimage.md)
   * [CocoaPods](nsoperationqueueloadpictures/cocoapods.md)
* [0712RunLoop](0912runloop/README.md)
   * [RunLoop-codes](0912runloop/runloop-codes.md)
* [0712NSURLConnection](0712nsurlconnection/README.md)
   * [520it.server](0712nsurlconnection/520itserver.md)
   * [URLConnection](0712nsurlconnection/urlconnection.md)
* [0713JSON/XML/](0713jsonxml/README.md)
   * [JSON](0713jsonxml/json.md)
   * [video](0713jsonxml/video.md)
   * [XML](0713jsonxml/xml.md)
   * [MJExtension](0713jsonxml/mjexetension.md)
   * [多值参数](0713jsonxml/morevalues.md)
   * [smallDownload](0713jsonxml/smalldownload.md)
   * [bigDownload](0713jsonxml/bigdownload.md)
   * [解压缩](0713jsonxml/archiverun.md)
   * [upLoad](0713jsonxml/upload.md)
   * [MIMEType](0713jsonxml/mimetype.md)
* [0715-NSURLSession/AFN](0715-nsurlsessionafn/README.md)
   * [outputStream](0715-nsurlsessionafn/outputstream.md)
   * [NSURLConnection&RunLoop](0715-nsurlsessionafn/nsurlconnection&runloop.md)
   * [NSURLSession](0715-nsurlsessionafn/nsurlsession.md)
   * [NSURLSession-大文件断点下载](0715-nsurlsessionafn/nsurlsession-upload.md)
   * [AFNetworking](0715-nsurlsessionafn/afnetworking.md)
   * [MD5&Https](0715-nsurlsessionafn/md5&https.md)
       * [https:-code](0715-nsurlsessionafn/https-code.md)
   * [UIWebView](0715-nsurlsessionafn/uiwebview.md)
       * [webViewDelegate](0715-nsurlsessionafn/wevviewdelegate.md)
       * [htmlSimpleExeample](0715-nsurlsessionafn/html.md)
* [JS<->OC,Big-download](uiwevviewjs-oc,big-download/README.md)
   * [js调oc](uiwevviewjs-oc,big-download/js-oc.md)
       * [oc调js](uiwevviewjs-oc,big-download/oc-js.md)
   * [NSInvocation-NSMethodSignature](uiwevviewjs-oc,big-download/nsinvocation-nsmethodsignature.md)
   * [handle-ERROR](uiwevviewjs-oc,big-download/handle-error.md)
   * [DownLoadOffLine](uiwevviewjs-oc,big-download/downloadoffline.md)
   * [RunNetSummary](uiwevviewjs-oc,big-download/runnetsummary.md)
* [0719-SVN](0719-svn/README.md)
   * [UNIX常用命令](0719-svn/unix_command.md)
   * [File Status](0719-svn/file_status.md)
   * [My_Questions](0719-svn/my_questions.md)
   * [visionSVN&备课笔记](0719-svn/visionsvn.md)
   * [SVNPPT](0719-svn/svnppt.md)
* [0720-GIT](0720-git/README.md)
   * [personal-CMD](0720-git/personal-cmd.md)
   * [group-CMD](0720-git/group-cmd.md)
* [百思不得姐](sister-think/README.md)
   * [发布程序到cocoapods](sister-think/pod_trunk.md)
   * [basicSetting](sister-think/basicsetting.md)
   * [setTabBar](sister-think/settabbar.md)
   * [appearanceTabBar](sister-think/appearance.md)
   * [setChildViewController](sister-think/setchildviewcontroller.md)
   * [tabBarFrame-Change](sister-think/tabbarframe-change.md)
   * [UIBarButtonItem的分类](sister-think/lmjnavgationcontroller.md)
   * [LMJNavgationVC-Push-backBarButtonItem](sister-think/lmjnavgationvc-push-backbarbuttonitem.md)
   * [##recommendVc-2tableViews](sister-think/recommendvc-2tableviews.md)
   * [MJRefresh-full](sister-think/mjrefresh-full.md)
   * [recommendTagCell](sister-think/recommendtagcell.md)
   * [returnKey & keyboardToolBar](sister-think/returnkey_&_keyboardtoolbar.md)
   * [登陆运行时textfiled-超出屏幕的frame设置](sister-think/lmjregistrlogin.md)
   * [Exssence-ScrollView-Drag&click](sister-think/exssence-scrollview-drag&click.md)
   * [LMJTopicViewController](sister-think/lmjtopicviewcontroller.md)
   * [LMJTopicCell](sister-think/lmjtopiccell.md)
       * [add contentTEXT, ](sister-think/add_contenttext,.md)
       * [add PictureView](sister-think/add_pictureview.md)
           * [LMJTopicPictureView](sister-think/lmjtopicpictureview.md)
       * [LMJTopicVoice&VideoView](sister-think/lmjtopicvoice&videoview.md)
       * [hotComment](sister-think/hotcomment.md)
   * [publishVc](sister-think/publishvc.md)
   * [statusBarHUD](sister-think/statusbarhud.md)
   * [LMJcommentVc](sister-think/lmjcommentvc.md)
   * [topWindowTapToTop](sister-think/topwindowtaptotop.md)
   * [UIMenuViewController](sister-think/uimenuviewcontroller.md)
   * [imageRoundly](sister-think/imageroundly.md)
   * [tabBar-doubleClickToRefresh](sister-think/tabbar-doubleclicktorefresh.md)
   * [新帖控制器](sister-think/newvc.md)
   * [LMJMeVc](sister-think/lmjmevc.md)
   * [LMJPublishVc](sister-think/lmjpublishvc.md)
   * [Word2AddTag](sister-think/word2addtag.md)
   * [Runtime-Method Swizzle](sister-think/runtime-method_swizzle.md)


