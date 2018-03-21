## 部署到Heroku(主机)和mLab(MongoDB数据库服务商)

Heroku和mLab提供了快捷的方法去部署Parse Server, 特别是对于服务端管理的新手很方便.

这是部署步骤:

1. 创建ParseServer中间件并挂载到你的Express应用(你可以使用我们的[示例工程](https://github.com/parse-community/parse-server-example), 也可以创建你自己的).
2. 创建一个Heroku账户(如果你之前没有的话) 并且使用Heroku Toolbelt(工具链)在你的Express应用目录创建一个新的应用. 查看[Getting Started with Node.js guide](https://devcenter.heroku.com/articles/getting-started-with-nodejs#introduction)了解更多.
3. 使用mLab插件: `heroku addons:create mongolab:sandbox` (或者你可以自己直接去mLab创建一个MongoDB实例)
4. 使用heroku配置工具并找到mLab提供的MONGOLAB_URI 
5. 复制这个URI并且创建一个新的环境变量: heroku config:set `DATABASE_URI=mongodb://...`
6. 部署项目代码: `git push heroku master`

你可以去Heroku开发者中心[Deploying a Parse Server to Heroku](https://devcenter.heroku.com/articles/deploying-a-parse-server-to-heroku)了解更多.
