#### 为推送消息注册设备

打开你的 `AppDelegate.swift`, `AppDelegate.m`, 或 `AppDelegate.cs` 文件并在 `application:didFinishLaunchingWithOptions:` 方法中添加如下内容:

```swift
// Swift
let types: UIUserNotificationType = [.Alert, .Badge, .Sound]
let settings = UIUserNotificationSettings(forTypes: types, categories: nil)
application.registerUserNotificationSettings(settings)
application.registerForRemoteNotifications()
```

```objc
// Objective-C
UIUserNotificationType userNotificationTypes = (UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge |
                                                UIUserNotificationTypeSound);
UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:userNotificationTypes
                                                                         categories:nil];
[application registerUserNotificationSettings:settings];
[application registerForRemoteNotifications];
```

```csharp
// Xamarin
UIUserNotificationType notificationTypes = (UIUserNotificationType.Alert |
                                            UIUserNotificationType.Badge |
                                            UIUserNotificationType.Sound);
var settings = UIUserNotificationSettings.GetSettingsForTypes(notificationTypes,
                                                              new NSSet(new string[] { }));
UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
UIApplication.SharedApplication.RegisterForRemoteNotifications();

// Handle Push Notifications
ParsePush.ParsePushNotificationReceived += (object sender, ParsePushNotificationEventArgs args) => {
  // Process Push Notification payload here.
};
```

在你的主应用delegate中添加下面的内容存储设备token并为消息提醒处理界面响应:

```swift
// Swift
func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
    let installation = PFInstallation.currentInstallation()
    installation.setDeviceTokenFromData(deviceToken)
    installation.saveInBackground()
}

func application(application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSError) {
    if error.code == 3010 {
        print("Push notifications are not supported in the iOS Simulator.")
    } else {
        print("application:didFailToRegisterForRemoteNotificationsWithError: %@", error)
    }
}

func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject]) {
    PFPush.handlePush(userInfo)
}
```

```objc
// Objective-C
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
  // Store the deviceToken in the current installation and save it to Parse.
  PFInstallation *currentInstallation = [PFInstallation currentInstallation];
  [currentInstallation setDeviceTokenFromData:deviceToken];
  [currentInstallation saveInBackground];
}

- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
  [PFPush handlePush:userInfo];
}
```

```csharp
// Xamarin
public override void DidRegisterUserNotificationSettings(UIApplication application,
    UIUserNotificationSettings notificationSettings) {
  application.RegisterForRemoteNotifications();
}

public override void RegisteredForRemoteNotifications(UIApplication application,
    NSData deviceToken) {
  ParseInstallation installation = ParseInstallation.CurrentInstallation;
  installation.SetDeviceTokenFromData(deviceToken);

  installation.SaveAsync();
}

public override void ReceivedRemoteNotification(UIApplication application,
    NSDictionary userInfo) {
  // We need this to fire userInfo into ParsePushNotificationReceived.
  ParsePush.HandlePush(userInfo);
}
```

##### 编译并运行!

如果你正确配置了, 当你运行应用的时候installation对象将会自动保存到Parse Server.可以运行下面的curl命令测试:

```curl
curl -X GET \
  -H "X-Parse-Application-Id: YOUR_APP_ID" \
  -H "X-Parse-Master-Key: YOUR_MASTER_KEY" \
  http://your_parse_server:1337/parse/installations
```

##### 进入[步骤 4](http://docs.parseplatform.cn/parse-server/guide/#4-发送推送消息).
