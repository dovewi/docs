# 消息推送

Parse Server为iOS, macOS, tvOS 和 Android 等系统提供基础的推送服务. 通过这个特性, 你可以:

* 通过操作系统平台选择目标设备
* 通过`ParseQuery`查询选择目标设备
* 通过[谷歌云推送(GCM)](https://developers.google.com/cloud-messaging/)推送消息到安卓设备
* 通过[苹果推送服务(APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)推送消息到苹果设备
* 使用大多数推送选项

你应该注意以下附加说明:

* 自从不使用任务队列系统之后无法保证高吞吐率了
* 客户端发送推送消息不支持. 你只能使用`masterKey`来发送推送消息
* 不支持到达反馈
* 不支持推送调度

## API
我们支持Parse.com服务的大多数原有推送设置选项. 点击[此处]({{ site.baseUrl }}/rest/guide/#sending-options)了解详情. Parse Server具体支持如下:

* `channels` 根据渠道选择目标设备
* `where` 根据`ParseQuery`查询选择目标设备
* `alert` 在`data`下推送消息
*  number `badge` 在`data`下iOS badge数字
* `sound` 在`data`下iOS音频
* `content-available` 在`data`下iOS后台任务
* `category` 在`data`下iOS分类
* `title ` 在`data`下安卓推送标题
* `uri ` 在`data`下安卓通知启动URI
* 在`data`下用户针对ios和安卓的自定义数据
* 针对ios和安卓的`data`里面自增`badge`数字

下面是我们暂时还不支持的推送选项:
* `push_time` 用来调度推送的选项


## 推送消息快速入门

### 1. 准备 APNS 和 GCM 证书

要发送推送消息,你必须要有GCM和APNS的证书.

#### APNS (iOS)

如果你准备第一次推送消息到iOS, tvOS 或 macOS等平台, 我们推荐你访问[raywenderlich.com's Push Notifications tutorial](https://www.raywenderlich.com/123862/push-notifications-tutorial) 或者 [appcoda.com's iOS Push tutorial](https://www.appcoda.com/push-notification-ios/) 了解如何获得苹果推送证书. Parse Server支持Keychain Access导出的PFX (`.p12`)文件格式. Parse Server也支持`.pem`格式的证书文件. 我们同时也支持基于Token而不是证书的授权.

#### GCM (Android)

要获得GCM的发送者ID, 访问[谷歌开发者控制台](https://console.developers.google.com)为你的安卓项目开启GCM. 记下你的项目好. 一般来说是一个大于123427208255的数字. 这个数字就是你的GCM发送者ID.

要获得GCM API key, 浏览[谷歌开发者证书](https://console.developers.google.com/apis/credentials)页面, 创建一个新的API KEY或者重用已有的都可以.

(此处有一段迁移原有Parse.com服务推送功能的内容因为对新用户没有什么用,浪费翻译时间被译者删除)

### 2. 配置Parse Server

在初始化Parse Server对象的时候, 你需要添加一个推送的配置. 例如

```js
var server = new ParseServer({
  databaseURI: '...',
  cloud: '...',
  appId: '...',
  masterKey: '...',
  push: {
    android: {
      senderId: '...',
      apiKey: '...'
    },
    ios: {
      pfx: '/file/path/to/XXX.p12',
      passphrase: '', // optional password to your p12/PFX
      bundleId: '',
      production: false
    }
  }
});
```

配置格式是:

```js
push: {
  android: {
    senderId: '', // GCM发送者ID
    apiKey: '' // GCM API KEY
  },
  ios: {
    pfx: '', // PFX or PKCS12私钥路径
    passphrase: '', // 可选p12密码
    cert: '', // 如果不使用.p12格式, PEM证书路径
    key: '', // 如果不适用.p12格式, PEM私钥路径
    bundleId: '', // The bundle identifier associated with your app
    production: false // Specifies which APNS environment to connect to: Production (if true) or Sandbox
  }
}
```

对于iOS, 如果你不使用证书,使用基于Token的认证,就使用下面的配置格式

```
push: {
  ios: {
    token: {
      key: '/file/path/to/AuthKey_XXXXXXXXXX.p8',
      keyId: "XXXXXXXXXX",
      teamId: "YYYYYYYYYY" // 苹果开发者账号团队ID
    },
    topic: 'com.domain.appname', // The bundle identifier associated with your app
    production: false
  }
}
```

如果你想同时支持测试和发布证书, 你可以用一个数组来进行配置,如下

```js
push: {
  ios: [
    {
      pfx: '', // Dev PFX or P12
      bundleId: '',
      production: false // Dev
    },
    {
      pfx: '', // Prod PFX or P12
      bundleId: '',  
      production: true // Prod
    }
  ]
}
```

macOS和tvOS的配置格式跟iOS一样. 只要添加对应的配置即可. macOS在配置里面要填`osx`. 如果你要同时支持测试和发布证书,同样可以像上面用数组来配置.

```js
var server = new ParseServer({
  databaseURI: '...',
  cloud: '...',
  appId: '...',
  masterKey: '...',
  push: {
    android: {
      senderId: '...',
      apiKey: '...'
    },
    ios: {
      pfx: '/file/path/to/XXX.p12',
      passphrase: '', // optional password to your p12/PFX
      bundleId: '',
      production: false
    },
    osx: {
      pfx: '/file/path/to/XXX.p12',
      passphrase: '', // optional password to your p12/PFX
      bundleId: '',
      production: false
    },
    tvos: {
      pfx: '/file/path/to/XXX.p12',
      passphrase: '', // optional password to your p12/PFX
      bundleId: '',
      production: false
    }
  }
});
```

如果你配置了多个证书(数组一样的配置), Parse Server的选择策略是首先尝试匹配客户端设备的`appIdentifier` 跟 `bundleId`. 如果可以找到有效证书,将会使用对应的证书发送苹果通知消息. 如果找不到, 将会使用所有的证书都发送一遍. 首先使用发布证书推送然后是测试证书.

### 3. 配置客户端应用

配置连接Parse Server的应用. 阅读[配置安卓和IOS客户端的步骤](#configuring-your-clients-to-receive-push-notifications)了解详情.

### 4. 发送推送消息

目前Parse Server只支持使用`masterKey`发送消息.最简单的方法使用curl发送请求:

```curl
curl -X POST \
  -H "X-Parse-Application-Id: you_app_id" \
  -H "X-Parse-Master-Key: your_master_key" \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "deviceType": {
            "$in": [
              "ios",
              "android"
            ]
          }
        },
        "data": {
          "title": "The Shining",
          "alert": "All work and no play makes Jack a dull boy."
        }
      }'\   http://your_server_address/parse/push
```

也可以使用cloud code发送推送消息:

```js
// With promises
Parse.Push.send({
  where: { ... },
  data: { ... }
}, { useMasterKey: true })
.then(function() {
  // Push sent!
}, function(error) {
  // There was a problem :(
});

// With Legacy Backbone callbacks
Parse.Push.send({
  where: query,
  data: {
    alert: 'Test',
    badge: 1,
    sound: 'default'
  }
}, {
  useMasterKey: true,
  success: function() {
    // Push sent!
  },
  error: function(error) {
    // There was a problem :(
  }
});
```

发送请求到Parse Server之后, 你将看到你的客户端设备显示通知.

**注意:** iOS模拟器不能接收推送通知. 你必须在真机运行才能看到.

在Parse Server的服务器日志中, 你将看到类似下面的内容

```json
GCM request and response {"request":{"params":{"priority":"normal","data":{"time":"2016-02-10T03:21:59.065Z","push_id":"NTDgWw7kp8","data":"{\"alert\":\"All work and no play makes Jack a dull boy.\"}"}}},"response":{"multicast_id":5318039027588186000,"success":1,"failure":0,"canonical_ids":0,"results":[{"registration_id":"APA91bEdLpZnXT76vpkvkD7uWXEAgfrZgkiH_ybkzXqhaNcRw1KHOY0s9GUKNgneGxe2PqJ5Swk1-Vf852kpHAP0Mhoj5wd1MVXpRsRr_3KTQo_dkNd_5wcQ__yWnWLxbeM3kg_JziJK","message_id":"0:1455074519347821%df0f8ea7f9fd7ecd"}]}}
```

```json
APNS Connected
APNS Notification transmitted to:7a7d2864598e1f65e6e02135245b7daf8ea510514e6376f072dc29d53facaa41
```

这些日志意味着GCM和APNS连接正常工作.

## Push Adapter

Parse Server提供`PushAdapter`抽象消息通知适配器. 默认的实现是`ParsePushAdapter`, 使用GCM和APNS. 如果你想使用其它的推送提供商,比如百度云推送等, 你可以自己实现一个`PushAdapter`.你的适配器必须要实现用来发送`data`到`installations`(设备)的`send(data, installations)`方法. 你可以参照 `ParsePushAdapter`. 当你实现了你自己的`PushAdapter`之后, 你可以使用如下配置设置你自己的适配器

```js
var server = new ParseServer({
  databaseURI: '...',
  cloud: '...',
  appId: '...',
  masterKey: '...',
  push: {
    adapter: your_adapter
  }
});
```

这么做之后, Parse Server将会调用你自己适配器的发送方法发送推送消息. 如果你提供了自己的`PushAdapter`, 默认的`ParsePushAdapter`将失效.

## 特性改进

目前的解决方案给出了一个很好的开始(推送功能才上线不久). 我们还有更多的改进特性待完成(没有完成所以不翻译了):

* Support more platforms
* Support more sending options
* Support more push providers
* Support scheduled pushes
* Support delivery report and error handling
* Support job queue and benchmarking

如果你对这些新特性感兴趣, 访问[don't hesitate to jump in and send a PR to the repo](https://github.com/parse-server-modules/parse-server-push-adapter). 希望和你一起完成这些功能!

## 备注

### 静默通知

(对于迁移Parse.com的应用的提醒不翻译)If you have migrated from Parse.com Push and you are seeing situations where silent notifications are failing to deliver, please ensure that your payload is setting the `content-available` attribute to Int(1) and not "1". This value will be explicitly checked.

### PPNS

* [PPNS Protocol Specification (for Parse IoT devices)](https://github.com/parse-community/parse-server/wiki/PPNS-Protocol-Specification)
