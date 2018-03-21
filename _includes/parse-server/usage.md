# 案例

Parse Server基于[Express](http://expressjs.com/)模块. Express是一个Node.js的Web框架. 最 简单的开始方法是克隆[Parse Server项目](https://github.com/parse-community/parse-server), 该项目的根节点包含一个挂载了Parse API的Express应用.

下面的构造函数返回与[Express中间件](http://expressjs.com/en/api.html#app.use)相一致的一个API对象. 该对象为Parse应用提供一个REST终端. 创建实例如下:

```js
var api = new ParseServer({
  databaseURI: 'mongodb://your.mongo.uri',
  cloud: './cloud/main.js',
  appId: 'myAppId',
  fileKey: 'myFileKey',
  masterKey: 'mySecretMasterKey',
  push: { ... }, // See the Push wiki page
  filesAdapter: ...,
});
```

参数如下:

* `databaseURI`: MongoDB连接URI.
* `cloud`: Cloud Code(云代码)代码文件位置.
* `appId`: 应用唯一标识符.
* `fileKey`: 文件存储定义的前缀. 对于迁移的APP访问原有Parse服务上迁移的文件来说这是必须的.
* `masterKey`: 可以获得所有权限的密钥. 必须保密(译者注:必须在服务端使用,泄露就完了).
* `clientKey`: 应用的客户端密钥. (可选)
* `restAPIKey`: 应用的REST接口密钥. (可选)
* `javascriptKey`: 应用的JavaScript KEY. (可选)
* `dotNetKey`: 应用的 .NET key. (可选)
* `push`: 推送配置对象. 参见 [推送](#push-notifications)
* `filesAdapter`: 一个实现 [FilesAdapter](https://github.com/parse-community/parse-server/blob/master/src/Adapters/Files/FilesAdapter.js) 接口的对象. 例如, [亚马逊S3文件适配器](#configuring-file-adapters)
* `auth`: 配置使用[第三方授权登陆](#oauth-and-3rd-party-authentication).

Parse Server对象可以直接在`app.use`中作为参数使用, 将在你的Express应用中把Parse API挂载到指定路径:

```js
var express = require('express');
var ParseServer = require('parse-server').ParseServer;

var app = express();
var api = new ParseServer({ ... });

// Serve the Parse API at /parse URL prefix
app.use('/parse', api);

var port = 1337;
app.listen(port, function() {
  console.log('parse-server-example running on port ' + port + '.');
});
```

运行上述代码, 你将拥有一个运行在1337端口,接口路径为`/parse`的Parse Server.
