# App Tracking Transparency

iOS 14 이상 지원하는 경우 [ATT (App Tracking Transparency) Framework](https://developer.apple.com/documentation/apptrackingtransparency?language=objc)를 적용해야 IDFA (Identifier for Advertisers) 식별자를 얻을 수 있습니다.

* iOS 14 이전에는 광고주가 IDFA (Identifier for Advertisers)를 사용하여 광고 성과 측정 및 맞춤형 광고를 할 수 있었습니다. 하지만 iOS 14 이상에서 ATT (App Tracking Transparency) 도입으로 인하여 개인 정보 보호가 강화됨에 따라, 사용자가 동의를 허용한 경우에만 IDFA 값을 가져올 수 있습니다.
* IDFA 접근 권한을 수동으로 획득하는 경우, 아래 절차를 따라주십시오.



{% hint style="info" %}
Admob UMP (UserMessaging Platform)를 활용하여 자동으로 ATT (App Tracking Transparency) 동의 알림을 처리하는 것이 권장됩니다.
{% endhint %}

{% hint style="danger" %}
<mark style="color:red;">**UMP를 이용한 IDFA 접근 권한 획득 방법(권장)**</mark> 이 있습니다.

***

이 문서의 [SDK 초기화](/broken/pages/L0k6H5Oot6mDjyhvaIe5) 단계에서 GDPR 메시지를 사용설정하는 경우 Admob UMP를 사용하게 됩니다.

Admob UMP의 GDPR 동의 화면이 보이는 상태에서 프로그래밍 방식을 사용하여 <mark style="color:red;">**수동으로 ATT (App Tracking Transparency) 동의 알림을 요청하는 경우 애플 앱 심사에서 거절될 수 있습니다.**</mark>
{% endhint %}



### 1. SKAdNetwork 구성

사용자의 ATT 동의 여부와 무관하게, 애플에서 공식으로 제공하는 광고 캠페인의 성공을 측정하기 위한 목적으로 [SKAdNetwork](https://developer.apple.com/documentation/storekit/skadnetwork/) 를 도입했습니다. SKAdNetwork 를 사용하기 위해 `Info.plist` 파일에 **광고 식별자 목록 정보를 추가**합니다.

{% hint style="info" %}
광고 네트워크에 지원되는 식별자 목록은 이 문서의 [SKAdNetwork ID List](/broken/pages/SMwG0MJNbHjd9bHg5slX) 에서 확인할 수 있습니다.
{% endhint %}



### 2. 권한 사용에 대한 설명 문구 추가

`Info.plist` 파일에 `NSUserTrackingUsageDescription` 키와 권한 사용에 대한 동의를 구하는 메시지를 추가합니다.

```xml
<key>NSUserTrackingUsageDescription</key>
<string>This identifier will be used to deliver personalized ads to you.</string>
```



### 3. 권한 요청

{% hint style="danger" %}
Admob UMP의 IDFA 메시지 기능 활성화 시, 별도의 코드를 추가하지 않아도, UMP 내부에서 자동으로 ATT 동의 알림 요청 기능을 수행하므로, 이 단계를 무시하십시오.
{% endhint %}

* ATT는 앱이 완전히 실행되어 Active 상태일 때 호출해주셔야 정상적으로 팝업이 노출됩니다.
  * `application:didFinishLaunchingWithOptions:`에서 ATT를 호출하고 있었다면, iOS 15 부터는 동작하지 않습니다.
* 사용자가 앱 추적 투명성 권한을 부여하면 광고 SDK에서 광고 요청에 IDFA를 사용할 수 있도록 완료 callback이 호출된 후, 광고를 요청합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
import AppTrackingTransparency

func requestIDFA() {
    if #available(iOS 14, *) {
        // ATT 알림을 통한 권한 요청
        ATTrackingManager.requestTrackingAuthorization(completionHandler: { status in
            switch status {
            case .authorized:       // 승인
                print("Authorized")
            case .denied:           // 거부
                print("Denied")
            case .notDetermined:        // 미결정
                print("Not Determined")
            case .restricted:           // 제한
                print("Restricted")
            @unknown default:           // 알려지지 않음
                print("Unknow")
            }
        })
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import <AppTrackingTransparency/AppTrackingTransparency.h>

- (void)requestIDFA {
    if (@available(iOS 14.5, *)) {
        // ATT 알림을 통한 권한 요청
        [ATTrackingManager requestTrackingAuthorizationWithCompletionHandler:^(ATTrackingManagerAuthorizationStatus status) {
            switch (status) {
                // 승인
                case ATTrackingManagerAuthorizationStatusAuthorized:
                    break;
                // 거부
                case ATTrackingManagerAuthorizationStatusDenied:
                    break;
                // 제한
                case ATTrackingManagerAuthorizationStatusRestricted:
                    break;
                // 미결정
                default:
                    break;
            }
        }];
    }
}
```
{% endtab %}
{% endtabs %}
