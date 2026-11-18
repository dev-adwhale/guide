# 3.4 테스트 기기 등록 for AdMob

{% hint style="warning" %}
* **AdMob 광고를 테스트 기기 상에서 테스트하고 싶을 경우에 테스트 기기를 등록하는 기능입니다.**
* 시뮬레이터는 기본적으로 테스트 기기이므로 기기 ID 목록에 추가할 필요가 없습니다.
* 앱을 출시하기 전에 테스트 기기 ID를 설정하는 코드를 삭제하거나 `#if DEBUG` 안에 두세요.
{% endhint %}

{% hint style="info" %}
개발 단계에서 광고를 테스트하려면 프로그래밍 방식으로 테스트 기기를 등록하세요.
{% endhint %}

1. AdWhale SDK를 탑재한 앱에서 광고를 한 번 요청합니다.
2. 로그 출력에서 다음 메시지를 확인합니다.

```
<Google> To get test ads on this device, set:
Objective-C
GADMobileAds.sharedInstance.requestConfiguration.testDeviceIdentifiers = @[ @"2077ef9a63d2b398840261c8221a0c9b" ];
Swift
MobileAds.shared.requestConfiguration.testDeviceIdentifiers = [ "2077ef9a63d2b398840261c8221a0c9b" ]
```

3. 테스트 기기 ID를 복사합니다.
4. 다음을 통해 테스트 기기 ID를 설정하도록 코드를 수정합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
AdWhaleMediationAds.setTestDeviceIdentifiers(["2077ef9a63d2b398840261c8221a0c9b"])
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
[AdWhaleMediationAds setTestDeviceIdentifiers:@[@"2077ef9a63d2b398840261c8221a0c9b"]];
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
여러 기기를 한 번에 등록할 수 있습니다. 등록한 기기 ID 는 앱이 실행 중일 때만 유지되며 재시작하면 초기화됩니다.
{% endhint %}
