# 2.6 App Transport Security

iOS 9부터 ATS([App Transport Security](https://developer.apple.com/documentation/bundleresources/information-property-list/nsapptransportsecurity))가 기본으로 켜져 있어, 앱은 HTTPS 통신만 할 수 있습니다.

* 일부 광고 소재와 광고 네트워크는 HTTP 리소스를 사용합니다. ATS 제한이 걸려 있으면 이런 광고가 로드되지 않거나 표시되지 않을 수 있습니다.

`Info.plist` 파일에 아래 내용을 추가합니다.

```xml
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsArbitraryLoads</key>
  <true/>
</dict>
```
