# 使用Parse SDK

要使用SDK, 修改下面案例的服务器URL到你自己的Parse服务器挂载的URL. 例如, 你将服务器安装在本地且挂载在/parse路径:

**iOS / OS X / watchOS / tvOS**

_Swift_

```swift
let configuration = ParseClientConfiguration {
    $0.applicationId = "YOUR_APP_ID"
    $0.clientKey = ""
    $0.server = "http://localhost:1337/parse"
}
Parse.initializeWithConfiguration(configuration)
```

_Objective-C_

```objc
[Parse initializeWithConfiguration:[ParseClientConfiguration configurationWithBlock:^(id<ParseMutableClientConfiguration> configuration) {
   configuration.applicationId = @"YOUR_APP_ID";
   configuration.clientKey = @"";
   configuration.server = @"http://localhost:1337/parse";
}]];
```

**Android**

```java
Parse.initialize(new Parse.Configuration.Builder(myContext)
    .applicationId("YOUR_APP_ID")
    .server("http://localhost:1337/parse/")
    ...
    .build()
);
```

**JavaScript**

```js
Parse.initialize("YOUR_APP_ID");
Parse.serverURL = 'http://localhost:1337/parse'
```

**.NET**

```csharp
ParseClient.initialize(new ParseClient.Configuration {
    ApplicationId = "YOUR_APP_ID",
    Server = "http://localhost:1337/parse/"
});
```

**PHP**

```php
ParseClient::initialize('YOUR_APP_ID', 'YOUR_CLIENT_KEY', 'YOUR_MASTER_KEY');
ParseClient::setServerURL('http://localhost:1337', 'parse'); // server url & mount path passed separately
```
