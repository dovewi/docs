#### 配置广播接收者和权限

添加下面的服务和广播接收者到`AndroidManifest.xml`文件中`</application>`标签之前:

```xml
<service android:name="com.parse.PushService" />
<receiver android:name="com.parse.ParsePushBroadcastReceiver"
android:exported="false">
<intent-filter>
<action android:name="com.parse.push.intent.RECEIVE" />
<action android:name="com.parse.push.intent.DELETE" />
<action android:name="com.parse.push.intent.OPEN" />
</intent-filter>
</receiver>
<receiver android:name="com.parse.GcmBroadcastReceiver"
android:permission="com.google.android.c2dm.permission.SEND">
<intent-filter>
<action android:name="com.google.android.c2dm.intent.RECEIVE" />
<action android:name="com.google.android.c2dm.intent.REGISTRATION" />

<!--
重要: 修改 "com.parse.starter" 到你自己应用的包名.
-->
<category android:name="com.parse.starter" />
</intent-filter>
</receiver>

<!--
重要: 修改 "YOUR_SENDER_ID" 到你自己的GCM发送者Id.
-->
<meta-data android:name="com.parse.push.gcm_sender_id"
  android:value="id:YOUR_SENDER_ID" />;
```

修改`<category>`中`android:name` 属性到你自己的包名.

修改"YOUR_SENDER_ID"到你在步骤1中获得GCM发送者ID. 浏览我们的[Android推送简介]({{ site.baseUrl }}/android/guide/#setting-up-push)查看更多细节.

(迁移应用的内容被译者删除)需要了解的请查阅英文版文档

在`<application>`标签前面添加下面的权限:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<!--
GET_ACCOUNTS is only required for GCM on devices running Android lower than
4.0.4. You may leave out this permission if you are targetting 4.0.4+.
-->
<uses-permission android:name="android.permission.GET_ACCOUNTS" />

<!--
IMPORTANT: Change "com.parse.starter.permission.C2D_MESSAGE" in the lines below
to match your app's package name + ".permission.C2D_MESSAGE".
-->
<permission android:protectionLevel="signature"
  android:name="com.parse.starter.permission.C2D_MESSAGE" />
<uses-permission android:name="com.parse.starter.permission.C2D_MESSAGE" />
```

修改最后两行的`android:name`中的前面部分包名为你自己的包名.

#### 注册消息推送的设备

在Application的onCreate方法中创建一个`Installation`对象 

```java
// Native: Application.java
public void onCreate() {
  // ...
  ParseInstallation.getCurrentInstallation().saveInBackground();
}
```

```csharp
// Xamarin: Application.cs

// IMPORTANT: Change "parsexamarinpushsample" to match your namespace.
[Application(Name = "parsexamarinpushsample.ParseApplication")]
class ParseApplication : Application {
  // ...

  public override void OnCreate() {
    base.OnCreate();

    // ...

    ParsePush.ParsePushNotificationReceived += ParsePush.DefaultParsePushNotificationReceivedHandler;
  }
}
```

##### 编译并运行!

如果正确完成了上述配置, installation对象将在你运行应用之后自动保存到你的Parse Server服务端. 运行下面的curl命令测试:

```curl
curl -X GET \
  -H "X-Parse-Application-Id: YOUR_APP_ID" \
  -H "X-Parse-Master-Key: YOUR_MASTER_KEY" \
  http://your_parse_server:1337/parse/installations
```

##### 进入[步骤 4](http://docs.parseplatform.cn/parse-server/guide/#4-%E5%8F%91%E9%80%81%E6%8E%A8%E9%80%81%E6%B6%88%E6%81%AF).
