< [Back](README.md)

常见问题强调
========

201405
-----
* 一大段一大段的注释掉的代码是干什么啊？git 是干嘛的，版本控制是拿来干嘛的？你让我是删啊，还是删啊，还是删啊。

* 组件不用了倒是删掉啊。怕以后还用？git 是干嘛的？没添加？谁让你万年不  commit 的？多 commit 少 push！参看 CarPointing 的提交日志。

* 一组可以节约你大量时间的快捷键：选中，Command + E，Command + G [+ Shift]。

* 像 `- (NSArray *)getDataWithAscending:(BOOL)ascending` 这种的命名是不符合 Cocoa 风格的，get 开头的方法是无返回的，通过指针引用回传。


201304
-----
* 建议将 Storyboard 中的视图都用 segue 连接起来，即使实际页面间切换没用 segue。这样做的目的是增加 scene 间的关联性，让人明确知道界面间的关系。只把相关 scene 放在一起而不连线又是会比较模糊。（201405，这种 segue 请设置成 place holder 类型的，并给它设置一个 identifier 以消除警告）

201303
-----
* <s>视图必须正确设置 `autoresizingMask`</s>（201405，现在全改用 Auto Layout，如果用代码创建视图，autoresizingMask 仍不失为方便的工具）。

* 把一个 viewController 的 view 加入到另一个 viewController 的 view 时，必须用 `addChildViewController:` 方法把这两个 viewController 关联起来。

* 给界面贴图一定要保证图片比例正确，避免拉伸失真。举例给按钮设置背景，图片的大小跟按钮的一致吗？不一致是不是要设置拉伸啊。@2x的图贴了吗？

* 永远不要用 `CGRectMake(10, 10, 300, 200)` 这样的方式设置视图尺寸。如果你不得不强制设置视图位置的话，应该依靠现有视图和常量进行计算。比如：

```Objective-C
CGFloat margin = 10.f;
CGRect bounds = superView.bounds;
CGFloat toolbarHeight = toolbar.frame.size.height;
view.frame = CGRect(margin, margin, bounds.size.width - margin*2, bounds.size.height - margin*2 - toolbarHeight);
```

* 除非涉及 `UIWindow` 否则永远不要用 `UIScreen` 的尺寸。99%的情况都应参照父视图的尺寸。

* 任务下发后有问题及时沟通，缺啥要啥，不清楚及时沟通，别等人来问你时才说。

* 错误发生先看错误信息，错误是什么都没搞清就别问其他人了。

* 如果一个任务你确定完不成了，赶快求助，别怕丢人。光憋在那里也提高不了，凭白浪费生命。没人笑话你（就算有那你也得给自己提高的机会吧）。

之前一些问题强调
----
* 常量定义：const与指针

```
// 错误写法，指向常量的指针，BGBookTypeID 可以指向其他字符串
NSString const *BGBookTypeID = @"something";

// 与上面写法等价，但更明确
const NSString *BGBookTypeID = @"something";

// 正确的写法，指向不可改变的指针
NSString *const BGBookTypeID = @"something";

```

* 在一个方法中要不要调用 super 方法是个问题，需要看文档。比如 `UIViewController` 的 `viewWillAppear:` 方法就有如下描述：

  > If you override this method, you must call super at some point in your implementation.

（201405，指出了必须调用 super，有时还会约束在开头还是结尾）