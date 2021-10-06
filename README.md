# Extole Android SDK

This app provides source code examples of how to:
- send an event to Extole
- get a resource (text, url, data) describing a marketing campaign configured in Extole
- share via email through Extole
- share using Android native share

Screenshot:

[<img src="https://user-images.githubusercontent.com/304224/130804856-ee7b5404-4a8d-4975-ad17-85cc0bf6e253.png" width="150">](https://github.com/extole/android-sdk/blob/master/app/src/main/java/com/extole/androidsdk/MainActivity.kt)

## Setup

1. Clone this repository
2. Open it with Android Studio
3. Run it in a simulator or real device

Note: dependencies for this project are not published to a public repository at this moment.

## Using the Extole SDK

### Repository

To your project `build.gradle` under the `repositories` configuration, add:

```
maven {
    name "github"
    url "https://maven.pkg.github.com/extole/android-sdk"
}
```

### Dependencies

On your application `build.gradle` file Add a dependency on `Extole Android SDK`:

```
implementation 'com.extole.mobile:android-sdk:1.0.0'
```

### Project configuration

Modify `AndroidManifest.xml`, add:

```
<meta-data
  android:name="com.extole.PROGRAM_DOMAIN"
  android:value="YOUR_PROGRAM_DOMAIN" />

<receiver
  android:name=".android.sdk.impl.ExtoleShareBroadcastReceiver"
  android:exported="false" />
```

### Initializing SDK
We recommend that you keep a reference to the `extole` and share it between activities.

```
val extole = Extole.builder()
    .withPersistance(SharedPreferencesPersistance(context))
    .withAppName("YOUR-APPLICATION-NAME")
    .addData("version", "1.0")
    .withDebugEnabled(true)
    .build()
```

### Rendering content for a menu item example

```
GlobalScope.launch {
    val (zone, campaign) = extole.getZone("promotional_menu")
    runOnUiThread {
        findViewById<EditText>(R.id.promotional_menu_item).setText(
            zone.get("menu_text").toString(), TextView.BufferType.NORMAL
        )
    }
}
```

This example assumes you have configured the 'promotional_menu' zone with 'menu_text' in Extole.

### WebView example

```
GlobalScope.launch {
    val (zone, campaign) = extole.getZone("promotional_menu")
    runOnUiThread {
        val menuItemButton = findViewById<Button>(R.id.menu_item_button)
        menuItemButton.text = zone.get("menu_item_text").toString()
        menuItemButton.setOnClickListener {
            val intent = Intent(this@Activity, WebViewActivity::class.java)
            intent.putExtra("webViewItem", menuItemButton.get("webViewItem").toString())
            startActivity(intent)
        }
    }
}
```

Where `WebViewActivity` layout contains a `WebView` and when Activity is initilized we are doing:

```
val extras = intent.extras
val webViewItem = extras!!.getString("webViewItem").orEmpty()

val webView = findViewById<WebView>(R.id.webview)
val context: Context = this
GlobalScope.launch {
    val webViewBuilder = extole.webViewBuilder(webView)
    runOnUiThread {
        val extoleWebView = webViewBuilder.create()
        extoleWebView.load(shareZoneName)
    }
}
```

A webview is a convenient way to support a rich campiagn experience, while minimizing mobile development. Extole,
supports tracking important events in the webview, like tracking native sharing events.

### Sending events

```
extole.sendEvent("purchase", mapOf("partner_user_id" to "123", "cart_value" to "120.30"))
```

### Sharing via Email through Extole

```
 GlobalScope.launch {
    val (_, campaign) = extole.getZone("promotional_email")
    campaign.emailShare(recipient, subject, message);
}
```
