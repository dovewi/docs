# 新手入门

Note that we support Android 2.3 and higher. You can also check out our [API Reference]({{ site.apis.android }}) for more detailed information about our SDK.

## Installation
**Step 1:** Download `Parse-SDK-Android`

Add dependency to the application level `build.gradle` file.

```groovy
dependencies {
  compile 'com.parse:parse-android:1.15.8'
}
```

**Step 2:** Setup Parse

- **Option 1:** Setup in the Manifest

You may define `com.parse.SERVER_URL` and `com.parse.APPLICATION_ID` meta-data in your `AndroidManifest.xml`:

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

Initializing Parse in the `Application`

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

- **Option 2:** Setup in the `Application`

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

 For either option, the custom `Application` class must be registered in `AndroidManifest.xml`:
 ```xml
 <application
   android:name=".App"
   ...>
   ...
 </application>
 ```
 
 **Option 3:** Setup permissions in the Manifest

You have to define `INTERNET`permissions in your `AndroidManifest.xml`:

```xml
<manifest ...>

    <uses-permission android:name="android.permission.INTERNET" />

    <application ...>
      ...
    </application>
</manifest>
```
