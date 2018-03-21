# 密钥

Parse Server 不必非要使用客户端的密钥. 包括客户端KEY, JavaScript key, .NET key, 以及REST API key. Application ID 足够保证应用的安全性.

无论如何, 你都可以定义上述的几个KEY. 如果定义了, Parse Server将会确保对应的客户端KEY和服务端配置的KEY一致. 行为和原Parse服务一致.

## 只读 masterKey

自从 `parse-server` 2.6.5版本开始, 你可以定义一个`readOnlyMasterKey`. 当使用只读KEY而不是masterKey的时候, 所有读取操作正常,但是所有写操作将会失败.

当你使用`parse-dashboard`的时候,这个KEY将会非常有用. 查看[Parse Dashboard项目](https://github.com/parse-community/parse-dashboard/)了解更多.
