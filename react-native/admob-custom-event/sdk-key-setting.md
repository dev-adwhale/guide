# 3. SDK 연동 키 설정

{% hint style="info" %}
AndroidManifest.xml 파일에 \<application>에  \<meta-data> 태그를 추가합니다.
{% endhint %}

```xml
<!-- AndroidManifest.xml -->
<application>
        ...
        <meta-data
            android:name="net.adwhale.sdk.mediation.PUBLISHER_UID"
            android:value="발급받은 PUBLISHER_UID" />
            
        <meta-data
            android:name="com.google.android.gms.ads.APPLICATION_ID"
            android:value="애드웨일이 제공한 Admob APP ID 값" />            
        ...
</application>
```

{% hint style="info" %}
* 각 광고 마다 발급 받은 **placementUid**가 필요합니다.
* 발급 받은 **placementUid**는 Custom event 추가 과정에 따라 Parameter에 입력하게 됩니다.
{% endhint %}
