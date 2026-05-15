# 액티비티 Orientation 설정

{% hint style="info" %}
액티비티 형식의 랜딩을 지원하기 위해서는 아래의 설정을 추가하여야 합니다.

만약 설정하지 않으면 화면 전환 시 마다 view가 초기화 됩니다.
{% endhint %}

```xml
<!-- AndroidManifest.xml -->
<activity
    android:name=".MainActivity"
    android:configChanges="keyboard|keyboardHidden|orientation|screenSize">
</activity>
```
