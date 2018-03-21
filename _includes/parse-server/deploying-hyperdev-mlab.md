## 部署到HyperDev和mLab

开始之前, 你需要:

 - GitHub 账号 (为了使用HyperDev导入功能)
 - mLab 账号 (提供免费MongoDB)

### 步骤 1: 去mLab创建一个数据库

[mLab](https://mlab.com) 提供Database-as-a-Service(数据库即服务)的MongoDB服务. 提供一个小型的免费运行于沙盒的数据库. 创建账户后使用Single-node(单节点), Sandbox plan(沙盒计划)去获得并运行一个免费的数据库.  使用mLab向导, 你可以创建一个有权限访问和连接新数据库的用户. 完成之后, 你将可以组合成类似下面的Mongo DB连接字符串:

```
mongodb://yourusername:yourpassword@yourmlabdatabaseaddress.mlab.com:yourdatabaseport/yourdatabasename
```

### 步骤 2: 在HyperDev运行parse-server-example项目

[HyperDev](https://hyperdev.com)提供免费空间部署Node.js应用.  我们将在上面部署[parse-server-example](https://github.com/parse-community/parse-server-example)这个项目.

通过hyperdev.com 你将获得一个随机的沙盒应用(名称通常类似 ```noun-noun```).  使用Github账号登陆HyperDev.然后在"Advanced Options"(高级选项)菜单种导入Github的项目.当提示导入的时候点确定即可

```
parse-community/parse-server-example
```

导入完成之后, 我们需要配置应用连接mLab的数据库.  修改用来存储环境变量的 ```.env``` 文件.

使用下面的模板:

```
# Environment Config

# store your secrets and config variables in here
# only invited collaborators will be able to see your .env values

# reference these in your code with process.env.SECRET

APP_ID=myAppId
MASTER_KEY=your_master_key_here
DATABASE_URI=mongodb://yourusername:yourpassword@yourmlabdatabaseaddress.mlab.com:yourdatabaseport/yourdatabasename

#your SERVER_URL will depend on whatever URL hyperdev/glitch gives you
SERVER_URL=https://noun-noun.glitch.me/parse

#hyperdev will only let you log to this folder
PARSE_SERVER_LOGS_FOLDER=/tmp

# note: .env is a shell file so there can't be spaces around '=
```

重要提示, 在这个教程中, 把 ```APP_ID``` 设置成了 ```myAppId``` 是因为测试页面硬编码设置成了这个值,如果你要修改而且也要使用测试页面的话,记得把测试页面的应用ID也一起改了.

### 步骤 3: 测试

当你修改完成 .env 文件之后, HyperDev将会自动编译部署你的应用.  如果你使用HyperDev的日志功能 (里面有个日志按钮), 你可以看到你的应用部署的输出信息:

```
Launching node index.js
parse-server-example running on port 3000.
```

之后你可以使用"Show"(演示)按钮在浏览器中启动并查看. 用浏览器访问挂载的```/test```路径.你可以看到测试页面并测试API调用结果,后面的内容没多大用处需要的可以自行去百度翻译. This should take you to a page that will allow you to exercise a few parts of the Parse Server Javascript SDK and create a dummy collection and record in your MongoDB.  If you're able to completes steps one through three on this test page, Parse Server is up and running.  Optionally, you can go back to mLab.com and take a look at the data that was stored by the test harness to get a feel for how Parse Server stores data in MongoDB.
