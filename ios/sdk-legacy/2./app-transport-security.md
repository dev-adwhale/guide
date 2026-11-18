# App Transport Security

iOS 9 에서 ATS ([App Transport Security](https://developer.apple.com/documentation/bundleresources/information_property_list/nsapptransportsecurity/))를 지원합니다. 또한 2017년 1월 이후 ATS를 활성화한 앱에 대해서만 앱스토어에 등록할 수 있도록 한 애플 정책이 수립되었습니다.

* 기기에서 ATS 활성화 시 암호화된 HTTPS 방식만 허용됩니다. HTTPS 방식을 적용하지 않을 경우 애플 보안 기준을 충족하지 않는다는 이유로 광고가 차단될 수 있습니다.

{% hint style="info" %}
Info.plist 파일에 아래 내용을 추가합니다.
{% endhint %}

```xml
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsArbitraryLoads</key>
  <true/>
</dict>
```
