# 네트워크 보안 구성

{% hint style="info" %}
**android9(API 28)부터 강화된 네트워크 보안정책으로 인해** \
**application에 값(android:usesCleartextTraffic=”true”)을 삽입합니다.** \
**광고 이미지 url load, 트래킹요소, resource가 http 구성될 수 있으므로 해당옵션을 삽입합니다.**
{% endhint %}

{% embed url="https://developer.android.com/privacy-and-security/security-config?hl=ko" %}

### 보안 정책 적용

**main/res/xml 하위에 아래 코드를 참고 하여 "network\_security\_config.xml" 생성합니다.**

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
```

**AndroidManifest.xml "application" 항목에 아래 코드를 참고 하여 적용합니다.**

```xml
<application
    ...    
    android:usesCleartextTraffic="true"
    android:networkSecurityConfig="@xml/network_security_config">
    ...
</application>
```
