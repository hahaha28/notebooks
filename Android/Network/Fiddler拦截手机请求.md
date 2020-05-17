# Fiddler 拦截手机请求

> [参考文章](https://wistbean.github.io/use-Fiddler.html)

## Fiddler 设置

1. 在`Fiddler`的`Tools -> Options-> Connections `打勾`Allow remote computers to connect`

2. `Tools -> Options-> Https `除了忽略证书错误都打勾

   可以点`Actions`导出证书等操作

3. 手机和电脑在同一个局域网下

4. 手机WIFI设置代理为电脑的IP+端口8888

5. 手机浏览器访问`IP:8888`下载证书，安装证书

## OkHttp 设置信任所有证书

我在自己手机上测试时，app发送`https`请求就闪退，报错显示证书验证错误，可能是因为`fiddler`的证书不靠谱，所以就在`OkHttp`中设置信任所有证书，这样就能用了。

```kotlin
val client = OkHttpClient.Builder()
	.sslSocketFactory(TrustAllCerts.createSSLSocketFactory(),TrustAllCerts())
	.hostnameVerifier(TrustAllCerts.TrustAllHostnameVerifier())
	.builder()
```

```java
import java.security.SecureRandom;
import java.security.cert.CertificateException;
import java.security.cert.X509Certificate;

import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLSession;
import javax.net.ssl.SSLSocketFactory;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;



public class TrustAllCerts implements X509TrustManager {
    @Override
    public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {}

    @Override
    public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {}

    @Override
    public X509Certificate[] getAcceptedIssuers() {return new X509Certificate[0];}


    public static SSLSocketFactory createSSLSocketFactory() {
        SSLSocketFactory ssfFactory = null;

        try {
            SSLContext sc = SSLContext.getInstance("TLS");
            sc.init(null,  new TrustManager[] { new TrustAllCerts() }, new SecureRandom());

            ssfFactory = sc.getSocketFactory();
        } catch (Exception e) {
        }

        return ssfFactory;
    }
    public static class TrustAllHostnameVerifier implements HostnameVerifier {
        @Override
        public boolean verify(String hostname, SSLSession session) {
            return true;
        }
    }

}
```

