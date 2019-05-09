---
title: Persisting Sensitive Data
---

凭据存储API允许您安全地存储敏感用户数据,如密码,服务器URL等.


## 如何使用

使用[`PasswordSafe`](upsource:///platform/platform-api/src/com/intellij/ide/passwordSafe/PasswordSafe.kt)处理凭据.


### Retrieve stored credentials
```java
    String key = null; // e.g. serverURL, accountID
    CredentialAttributes credentialAttributes = createCredentialAttributes(key);
    
    Credentials credentials = PasswordSafe.getInstance().get(credentialAttributes);
    if (credentials != null) {
      String password = credentials.getPasswordAsString();
    }

    // or get password only
    String password = PasswordSafe.getInstance().getPassword(credentialAttributes);    
    
    private CredentialAttributes createCredentialAttributes(String key) {
        return new CredentialAttributes(CredentialAttributesKt.generateServiceName("MySystem", key));        
    }    
```

###存储凭据


```java
    CredentialAttributes credentialAttributes = createCredentialAttributes(serverId); // see previous sample
    Credentials credentials = new Credentials(username, password);
    PasswordSafe.getInstance().set(credentialAttributes, credentials);
```
To remove stored credentials, pass `null` for `credentials` parameter.

##存储

默认存储格式取决于操作系统.


| OS      | Storage |
|---------|---------|
| Windows | File in [KeePass](https://keepass.info) format |
| macOS   | Keychain using [Security Framework](https://developer.apple.com/documentation/security/keychain_services) |
| Linux   | [Secret Service API](https://standards.freedesktop.org/secret-service) using [libsecret](https://wiki.gnome.org/Projects/Libsecret) |

Users can override the default behavior in Preferences \| Appearance & Behavior \| System Settings \| Passwords.


