# 3. SDK 초기화

{% hint style="info" %}
* `AdWhaleSDK`를 프로젝트에 추가해야 합니다.
* 광고를 요청하기 전에 초기화가 이루어져야 합니다.&#x20;
* 초기화를 담당하는 **`AdWhaleMediationAds` 클래스를 앱 시작 시점에 호출**하며, \
  호출 결과는 **완료 클로저 또는 `AdWhaleMediationOnInitCompleteDelegate` 를 통해 통지**됩니다.
{% endhint %}

### 초기화 순서

1. **사용할 어댑터 등록** — `register()`
2. **초기화** — `initialize(...)`
3. **완료 콜백(`statusCode == 100`) 이후에 광고 로드**

{% hint style="danger" %}
이 순서를 지키지 않으면 **오류 없이 광고만 나오지 않습니다.**

* `register()` 를 `initialize()` 뒤에 호출하면 그 네트워크는 워터폴에서 건너뛰어집니다.
* 완료 콜백 전에 광고를 로드하면 `200`(`SDK not initialized.`)이 통지됩니다.
{% endhint %}

### 1. 어댑터 등록

사용할 광고 네트워크의 어댑터를 코드에서 등록합니다.

| 광고 네트워크   | 등록 클래스                    |
| --------- | ------------------------- |
| AdMob     | `AdWhaleAdMobAdapter`     |
| Cauly     | `AdWhaleCaulyAdapter`     |
| AdFit     | `AdWhaleAdFitAdapter`     |
| Admize    | `AdWhaleAdmizeAdapter`    |
| LevelPlay | `AdWhaleLevelPlayAdapter` |

{% tabs %}
{% tab title="Swift" %}
```swift
// 사용할 네트워크의 어댑터만 import 하고 등록합니다
import AdWhaleSDK
import AdWhaleAdMobAdapter
import AdWhaleCaulyAdapter

AdWhaleAdMobAdapter.register()
AdWhaleCaulyAdapter.register()
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
// 사용할 네트워크의 어댑터만 import 하고 등록합니다
@import AdWhaleSDK;
@import AdWhaleAdMobAdapter;
@import AdWhaleCaulyAdapter;

[AdWhaleAdMobAdapter register];
[AdWhaleCaulyAdapter register];
```
{% endtab %}
{% endtabs %}

{% hint style="danger" %}
등록되지 않은 어댑터는 워터폴에 참여하지 않아, 서버가 그 네트워크를 내려줘도 오류 없이 건너뜁니다. \
로그에 `No banner adapter registered for network: …` 가 보이면 이 경우입니다.
{% endhint %}

### 2. Initialize

**`AdWhaleMediationAds` 클래스 API**

| API                                    | Publisher UID 출처 | 콜백 방식    |
| -------------------------------------- | ---------------- | -------- |
| `initialize(completion:)`              | Info.plist       | 클로저      |
| `initialize(publisherUid:completion:)` | 파라미터             | 클로저      |
| `initialize(delegate:)`                | Info.plist       | Delegate |
| `initialize(publisherUid:delegate:)`   | 파라미터             | Delegate |

{% tabs %}
{% tab title="Swift" %}
```swift
static func initialize(completion: @escaping (Int, String) -> Void)
static func initialize(publisherUid: String, completion: @escaping (Int, String) -> Void)
static func initialize(delegate: AdWhaleMediationOnInitCompleteDelegate)
static func initialize(publisherUid: String, delegate: AdWhaleMediationOnInitCompleteDelegate)
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
+ (void)initializeWithCompletion:(void (^)(NSInteger statusCode, NSString *message))completion;
+ (void)initializeWithPublisherUid:(NSString *)publisherUid
                        completion:(void (^)(NSInteger statusCode, NSString *message))completion;
+ (void)initializeWithDelegate:(id<AdWhaleMediationOnInitCompleteDelegate>)delegate;
+ (void)initializeWithPublisherUid:(NSString *)publisherUid
                          delegate:(id<AdWhaleMediationOnInitCompleteDelegate>)delegate;
```
{% endtab %}
{% endtabs %}

| 파라미터           | 설명                                                                                                                              |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| `publisherUid` | <p>발급받은 Publisher UID. <br><strong>생략하면 Info.plist 의 <code>net.adwhale.sdk.mediation.PUBLISHER_UID</code> 값을 사용합니다</strong></p> |
| `completion`   | 초기화 결과 클로저 `(statusCode, message)`                                                                                              |
| `delegate`     | 초기화 결과 통지 Delegate                                                                                                              |

**`AdWhaleMediationOnInitCompleteDelegate`**

{% tabs %}
{% tab title="Swift" %}
```swift
func onInitComplete(statusCode: Int, message: String)
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
- (void)onInitCompleteWithStatusCode:(NSInteger)statusCode message:(NSString *)message;
```
{% endtab %}
{% endtabs %}

***

### 3. 초기화 결과

| statusCode | message                                          | 설명                                                   |
| :--------: | ------------------------------------------------ | ---------------------------------------------------- |
|   **100**  | `Success!`                                       | 초기화 성공                                               |
|   **100**  | `Already initialized.`                           | 이미 초기화된 상태에서 재호출                                     |
|   **200**  | `Publisher UID not found. …`                     | Publisher UID 를 찾지 못했습니다. 파라미터 또는 Info.plist 를 확인하세요 |
|   **200**  | `Initialization failed. Device info is invalid.` | 디바이스 정보 수집에 실패했습니다                                   |

***

### 4. 초기화 여부 확인

{% tabs %}
{% tab title="Swift" %}
```swift
if AdWhaleMediationAds.initialized {
    // 광고 로드 가능
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
if ([AdWhaleMediationAds initialized]) {
    // 광고 로드 가능
}
```
{% endtab %}
{% endtabs %}

### **5. 초기화 샘플코드**

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK
import AdWhaleAdMobAdapter

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication,
                     didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

        // 1. 어댑터 등록 (initialize 보다 먼저) - 사용할 네트워크만 (AdMob 예시)
        AdWhaleAdMobAdapter.register()

        // 2. 초기화
        AdWhaleMediationAds.initialize { statusCode, message in
            if statusCode == 100 {
                // 초기화 성공 — 이 시점 이후에 광고를 로드하세요
            } else {
                print("SDK 초기화 실패 (\(statusCode)): \(message)")
            }
        }
        return true
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objectivec
#import "AppDelegate.h"
@import AdWhaleSDK;
@import AdWhaleAdMobAdapter;

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application
        didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    // 1. 어댑터 등록 (initialize 보다 먼저) - 사용할 네트워크만 (AdMob 예시)
    [AdWhaleAdMobAdapter register];

    // 2. 초기화
    [AdWhaleMediationAds initializeWithCompletion:^(NSInteger statusCode, NSString *message) {
        if (statusCode == 100) {
            // 초기화 성공 — 이 시점 이후에 광고를 로드하세요
        } else {
            NSLog(@"SDK 초기화 실패 (%ld): %@", (long)statusCode, message);
        }
    }];
    return YES;
}

@end
```
{% endtab %}
{% endtabs %}
