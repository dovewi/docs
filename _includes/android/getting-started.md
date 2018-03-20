# 新手入门

注意:SDK仅支持 Android 2.3 以上版本. 你可以查看 [API 文档]({{ site.apis.android }}) 了解SDK更多细节.

## 安装
**步骤 1:** 下载 `Parse-SDK-Android`

在应用模块的 `build.gradle` 文件.

```groovy
dependencies {
  compile 'com.parse:parse-android:1.15.8'
}
```

**步骤 2:** 设置 Parse

- **方法 1:** 通过Manifest设置

你可以在`AndroidManifest.xml`文件中定义 `com.parse.SERVER_URL` 和 `com.parse.APPLICATION_ID` 两个meta-data :

```xml
<application ...>
  <meta-data
    android:name="com.parse.SERVER_URL"
    android:value="@string/parse_server_url" />
  <meta-data
    android:name="com.parse.APPLICATION_ID"
    android:value="@string/parse_app_id" />
  ...
</application>
```

在 `Application`中初始化 Parse

```java
import com.parse.Parse;
import android.app.Application;

public class App extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    Parse.initialize(this);
  }
}
```

- **方法 2:** 在`Application`中设置

```java
import com.parse.Parse;
import android.app.Application;

public class App extends Application {
  @Override
  public void onCreate() {
    super.onCreate();
    Parse.initialize(new Parse.Configuration.Builder(this)
      .applicationId("YOUR_APP_ID")
      .server("http://localhost:1337/parse/")
      .build()
    );
  }
}
```

 对于上述方法, 自定义的 `Application` 类必须在 `AndroidManifest.xml`中注册:
 ```xml
 <application
   android:name=".App"
   ...>
   ...
 </application>
 ```
 
 **步骤 3:** 在Manifest中设置权限

你必须在 `AndroidManifest.xml`中注册`INTERNET`访问权限:

```xml
<manifest ...>

    <uses-permission android:name="android.permission.INTERNET" />

    <application ...>
      ...
    </application>
</manifest>
```
