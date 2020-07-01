# Tool Box Java <!-- omit in toc -->

- [Code Samples](#code-samples)
  - [Symmetric Key Generator](#symmetric-key-generator)
- [keytool](#keytool)
  - [Generating a self-signed certificate](#generating-a-self-signed-certificate)
  - [Import certificat in keystore](#import-certificat-in-keystore)

## Code Samples

### Symmetric Key Generator

```Java
    @Test
    public void symmetricKey() throws NoSuchAlgorithmException {
        KeyGenerator keyGen = KeyGenerator.getInstance("AES");
        keyGen.init(256);
        String encodedKey = Base64.getEncoder().encodeToString(keyGen.generateKey().getEncoded());
        System.out.println("encodedKey  " + encodedKey);
    }
```

## keytool

### Generating a self-signed certificate

```sh
$JAVA_HOME/bin/keytool -genkey -alias dev-server -keyalg RSA -storetype JKS -keypass keypwd -storepass storepwd -keystore keystore.jks -validity 3600 -dname "CN=dev-server, OU=Xxxx, O=Yyyyy, L=Zzzzz, ST=Wwwww, C=FR"
```

### Import certificat in keystore

```sh
$JAVA_HOME/bin/keytool -import -trustcacerts -file /tmp/hostname.cert -alias hostname -keystore keystore.jks
```
