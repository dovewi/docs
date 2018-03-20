# 新手入门

Parse Server是一个开源版本的Parse后端,可以部署于任何可以运行Node.js的基础设施.你可以在[GitHub repo](https://github.com/parse-community/parse-server)上找到源代码.

* Parse Server 不依赖于 Parse 后端.
* Parse Server 直接使用 MongoDB, 不依赖于 Parse 数据库服务.
* 你可以迁移你原有的基于 Parse 服务的应用到你自己的服务器后台.
* 你可以在本地使用Node搭建服务器调试.
* 译者注(原来Parse是一个基于云端的后端管理平台。对于开发者而言，Parse提供后端的一站式和一揽子服务：服务器配置、数据库管理、API、影音文件存储，实时消息推送、客户数据分析统计、等等。后公司被Facebook收购,项目被废,所以放出了一个可供无缝迁移的社区版,也就是Parse Server)

### 前提环境

* Node 4.3
* MongoDB 版本 2.6.X, 3.0.X or 3.2.X
* Python 2.x (对于Windows版本, 需要2.7.1)
* 为了部署, 需要一个类似 Heroku 或者 AWS 主机, 国内推荐阿里云或者腾讯云等等, 译者使用的新浪SAE部署, SAE的MongoDB服务比较便宜

### 和原 Parse 的兼容性

这儿有几处无法提供兼容的Parse后端服务,如果你迁移Parse.com的应用到Parse Server自建服务器,请花一些时间小心阅读如下[兼容性问题](#compatibility-with-parsecom).

入门最快捷的方式是在本地部署 MongoDB 和 Parse Server. 使用下面的启动脚本安装 Parse Server 到当前目录.

```bash
$ sh <(curl -fsSL https://raw.githubusercontent.com/parse-community/parse-server/master/bootstrap.sh)
$ npm install -g mongodb-runner
$ mongodb-runner start
$ npm start
```

你可以使用任意字符串作为你的 application id(应用ID) 和 master key(超级KEY). 这两个KEY将在客户端用来获得Parse Server的API调用授权.

好了! 你现在得到了一个运行在本地的Parse服务端.

## 保存你的第一个对象

现在你成功运行了Parse Server, 是时候保存你的第一个对象了(译者注:文档里面所说的对象,就是存在数据表中的一条数据). 我们将使用 [REST API]({{ site.baseUrl }}/rest/guide), 但是你也可以使用 [Parse SDKs]({{ site.baseUrl }}/)做同样的操作. 运行如下命令:

```bash
curl -X POST \
-H "X-Parse-Application-Id: APPLICATION_ID" \
-H "Content-Type: application/json" \
-d '{"score":123,"playerName":"Sean Plott","cheatMode":false}' \
http://localhost:1337/parse/classes/GameScore
```

你将获得类似下面的响应返回结果:

```js
{
  "objectId": "2ntvSpRGIK",
  "createdAt": "2016-03-11T23:51:48.050Z"
}
```

你也可以直接读取刚才存入的对象 (切记把 `2ntvSpRGIK` 换成你执行命令创建对象返回看到的 `objectId`):

```bash
$ curl -X GET \
  -H "X-Parse-Application-Id: APPLICATION_ID" \
  http://localhost:1337/parse/classes/GameScore/2ntvSpRGIK
```

```json
// Response
{
  "objectId": "2ntvSpRGIK",
  "score": 123,
  "playerName": "Sean Plott",
  "cheatMode": false,
  "updatedAt": "2016-03-11T23:51:48.050Z",
  "createdAt": "2016-03-11T23:51:48.050Z"
}
```

无论如何一直保存着对象的ID来进行读取也不是办法. 在绝大多数场景中你可以执行一个查询来获得你插入的对象, 就像下面这样:

```
$ curl -X GET \
  -H "X-Parse-Application-Id: APPLICATION_ID" \
  http://localhost:1337/parse/classes/GameScore
```

```json
// 响应会在 `results` 数组中返回所有匹配的数据:
{
  "results": [
    {
      "objectId": "2ntvSpRGIK",
      "score": 123,
      "playerName": "Sean Plott",
      "cheatMode": false,
      "updatedAt": "2016-03-11T23:51:48.050Z",
      "createdAt": "2016-03-11T23:51:48.050Z"
    }
  ]
}

```

了解更多保存和查询对象的相关内容, 查看 [documentation]({{ site.baseUrl }}/).

## 让App连接你的Parse服务器

Parse为所有主要的平台提供SDK. 阅读如下文档 [学习如何连接App到Parse Server服务器](#using-parse-sdks-with-parse-server).

## 在别处运行Parse Server

当你很好的理解了项目如何运行的时候, 阅读如下内容了解 [部署Parse到服务器](#deploying-parse-server) 以学习更多的运行Parse Server服务器的方式.
