# 1. 요구사항 및 사용권한

본 문서는 ADwhale Mediation SDK를 Flutter로 개발된 안드로이드 어플리케이션에 Admob custom event로 연동하기 위한 가이드 문서입니다. 연동전에 다음의 안내 사항을 반드시 읽어 주시기 바랍니다.

{% hint style="info" %}
본 문서는 최신 ADwhale Mediation SDK 버전을 기준으로 업데이트 되고 있습니다.

하위 버전의 기술지원은 별도 문의 바랍니다.
{% endhint %}

### **요구사항**

* 안드로이드 지원 버전
  * Android API Level 24 이상(Android 15 대응)
  * Android 5.0(API 21) 미만의 경우 multi-dex 적용 필요
* 안드로이드 타겟 버전 지원
  * API Level 33(Android 13 대응)
  * GooglePlay의 타겟 API 수준 요구사항 충족하기
* Support Library
  * AndroidX 대응
  * Android Support Library

### **앱 사용 권한(permission)**

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="com.google.android.gms.permission.AD_ID"/>
```

### MainActivity 클래스

{% hint style="info" %}
MainActivity 클래스 설정이 없을 경우 앱 실행에 문제가 발생합니다.

Android 5.0(API Level 21) 하위 버전을 지원하는 앱의 경우 Multidex 적용이 필요합니다.
{% endhint %}

ADwhale Mediation SDK 동작을 위해서는 MainActivity(메인 액티비티 / Launch, Main) 설정이 필요합니다.

{% hint style="info" %}
더 안전한 구성요소 내보내기 설정: Android 12(API Level 31) 이상

intent-filter를 사용하는 액티비티를 포함하는 경우 exported=true 적용이 필요합니다.
{% endhint %}

```xml
<!-- AndroidManifest.xml -->
<application>
    ...
    <activity
        android:name=".MainActivity"
        android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />

            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
    ...
</application>
```
