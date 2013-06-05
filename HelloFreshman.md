
优先学习方向
====

ARC、block、Storyboard、Core Data 这是最基本的，必须掌握，没什么好说的。

AFNetworking 是我们在用的网络基础库，一上来可能觉着复杂，其实用起来很简单。如果你之前熟悉 ASIHttpRequest 的话，要比 ASI 好用。80% 的情况，请求一个接口就像下面一样简单：

```
[self getPath:@"http://example.com/api/do" parameters:@{
    @"参数": 123
} success:^(AFHTTPRequestOperation *operation, id responseObject) {
    // 处理返回数据   
} failure:^(AFHTTPRequestOperation *operation, NSError *error) {
    // 处理错误
}];
```

接口请求都是封装在 API 模块中完成的，具体可以参看已有项目。

之后，几个重要的主题是 GCD、KVO。


关于已有代码
----

学习开发的一个重要途径就是阅读他人的代码，但是实际项目在进行中会有太多的限制，导致目前公司仓库里的代码很多不是学习的榜样。有两个重要原因：

1. 需求的变动。稍微大一点的项目基本都会有需求变动的情况，如果改变超出了预计，可能会产生不雅的修补，这种修补积聚而不能重构改善，就会导致项目改着改着就乱掉了。
2. 外包项目和做自己的产品是不一样的，外包项目不会给你时间精雕细琢，常常是“看上去”能用就OK了。
