# SDK 연동 키 설정

### 1. Publisher UID (필수)

***

Info.plist 에 애드웨일에서 발급 받은 Publisher UID 를 추가합니다.

```xml
<key>net.adwhale.sdk.mediation.PUBLISHER_UID</key>
<string>발급받은 애드웨일 PUBLISHER_UID 값</string>
```

{% hint style="danger" %}
Publisher UID 가 없으면 `initialize()` 가 **200(연동 오류)** 으로 실패합니다.
{% endhint %}

#### 코드에서 전달하는 방법

Info.plist 대신 초기화 파라미터로 넘길 수도 있습니다.

파라미터가 Info.plist 보다 우선하며, 파라미터가 공백이면 Info.plist 값으로 폴백합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleMediationAds.initialize(publisherUid: "발급받은 애드웨일 PUBLISHER_UID 값") { statusCode, message in
    if statusCode == 100 { print("SDK 초기화 성공") }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[AdWhaleMediationAds initializeWithPublisherUid:@"발급받은 애드웨일 PUBLISHER_UID 값"
                                     completion:^(NSInteger statusCode, NSString *message) {
    if (statusCode == 100) { NSLog(@"SDK 초기화 성공"); }
}];
```
{% endtab %}
{% endtabs %}

| 초기화 API                                                                            | Publisher UID 출처      |
| ---------------------------------------------------------------------------------- | --------------------- |
| initialize(completion:) / initialize(delegate:)                                    | Info.plist            |
| <p>initialize(publisherUid:completion:) <br>initialize(publisherUid:delegate:)</p> | 파라미터(공백이면 Info.plist) |

{% hint style="info" %}
빌드 구성별로 다른 UID 를 쓰거나 원격 설정에서 주입할 때 파라미터 방식이 유용합니다.
{% endhint %}

***

### 2. 애드몹 앱 ID(애드몹 사용 시 필수)

***

```xml
<key>GADApplicationIdentifier</key>
<string>애드웨일이 제공한 애드몹 앱 ID 값</string>
```

{% hint style="info" %}
`GADApplicationIdentifier` 를 추가하지 않으면 **Google Mobile Ads SDK 가 앱을 비정상 종료시킵니다.**\
애드몹 어댑터를 탑재했다면 반드시 설정하세요.
{% endhint %}

***

### 3.Placement UID

***

지면별 Placement UID 는 Info.plist 가 아니라 광고 객체에 직접 설정합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
bannerView.placementUid = "발급받은 PLACEMENT_UID 값"
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
bannerView.placementUid = @"발급받은 PLACEMENT_UID 값";
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
`placementUid` 를 설정하지 않으면 로드 실패 콜백이 **200(연동 오류)** 으로 옵니다.\
300 이 아니므로 재시도해도 채워지지 않습니다.
{% endhint %}

***
