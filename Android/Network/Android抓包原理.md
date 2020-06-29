# Android 抓包原理

> [转载自这篇文章](https://mp.weixin.qq.com/s/l13OLrXJbRrtUkQlV1q6fg)

<img src="http://img.inaction.fun/static/36373.png" alt="image" style="zoom:50%;" />

几乎所有的抓包软件都是采用中间人的方式

## 安装CA证书的方式

抓包软件内置的CA证书要生效，必须安装到系统中。Android 系统的CA证书分为用户CA证书和系统CA证书。用户CA证书是用户自己安装的，系统CA证书是系统内置的。

Android 从7.0 开始，系统不再信任用户的CA证书（应用targetSdkVersion >= 24时生效，如果targetSdkVersion < 24即使系统是7.0+依然会信任）。也就是说即使安装了用户CA证书，在Android 7.0+的机器上，targetSdkVersion >= 24的应用的HTTPS包就抓不到了。

下面有几种解决办法：

### 1. 配置networkSecurityConfig

如果我们想抓自己的App，只需要在AndroidManifest中配置networkSecurityConfig即可：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <application android:networkSecurityConfig="@xml/network_security_config"
       ... >
        ...
    </application>
</manifest>
```

>  /res/xml/network_security_config.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" />
            <certificates src="user" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

这样即表示，App信任用户CA证书，让系统对用户CA证书的校验给予通过。