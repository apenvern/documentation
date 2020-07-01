# Tool Box Spring <!-- omit in toc -->

- [Enabled Https with Spring Boot](#enabled-https-with-spring-boot)
  - [Configure Spring](#configure-spring)
  - [Problem](#problem)
  - [Disable SSL Certificat check with Spring Boot](#disable-ssl-certificat-check-with-spring-boot)

## Enabled Https with Spring Boot

### Configure Spring

```yml
server:
  port: 8443
  ssl:
    key-store: classpath:config/local/keystore.jks
    key-store-password: storepwd
    key-password: keypwd
    key-store-type: JKS
```

### Problem

#### Invalid keystore format

This problem can occur when resource filtering with Maven. When Maven filter resources, it will corrupt the key file. You must exclude the jks file for filtering

```java
java.io.IOException: Invalid keystore format
    at sun.security.provider.JavaKeyStore.engineLoad(JavaKeyStore.java:658)
    at sun.security.provider.JavaKeyStore$JKS.engineLoad(JavaKeyStore.java:56)
    at sun.security.provider.KeyStoreDelegator.engineLoad(KeyStoreDelegator.java:224)
    at sun.security.provider.JavaKeyStore$DualFormatJKS.engineLoad(JavaKeyStore.java:70)
    at java.security.KeyStore.load(KeyStore.java:1445)
```

```xml
   <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
        <excludes>
            <exclude>**/*.jks</exclude>
        </excludes>
    </resource>
    <resource>
        <directory>src/main/resources</directory>
        <filtering>false</filtering>
        <includes>
            <include>**/*.jks</include>
        </includes>
    </resource>

```

### Disable SSL Certificat check with Spring Boot

```java
import java.security.cert.CertificateException;
import java.security.cert.X509Certificate;

import javax.annotation.PostConstruct;
import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.HttpsURLConnection;
import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLSession;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;

import org.springframework.context.annotation.Configuration;

/**
 * Configuration de la console d'administration de l'application Alliances Locales.
 *
 */
@Configuration
public class AppConfiguration {

    /**
     * Désactivation du contrôle de vérification des certificats https.
     */
    @PostConstruct
    public static void disable() {
        try {
            SSLContext sslc = SSLContext.getInstance("TLS");
            TrustManager[] trustManagerArray = { new NullX509TrustManager() };
            sslc.init(null, trustManagerArray, null);
            HttpsURLConnection.setDefaultSSLSocketFactory(sslc.getSocketFactory());
            HttpsURLConnection.setDefaultHostnameVerifier(new NullHostnameVerifier());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * Implementation de De l'interface de vérification des certificat pour désactiver le contrôle du certificat.
     *
     */
    private static class NullX509TrustManager implements X509TrustManager {
        @Override
        public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {
        }

        @Override
        public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
        }

        @Override
        public X509Certificate[] getAcceptedIssuers() {
            return new X509Certificate[0];
        }
    }

    private static class NullHostnameVerifier implements HostnameVerifier {
        @Override
        public boolean verify(String hostname, SSLSession session) {
            return true;
        }
    }

}
```
