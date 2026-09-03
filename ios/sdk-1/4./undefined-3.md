# 보상형

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Reward Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

### RewardAd 설정

#### 1. 광고 객체 생성

보상형 광고를 사용하기 위해 `AdWhaleRewardAd` 객체를 생성합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
let rewardAd = AdWhaleRewardAd()
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
AdWhaleRewardAd *rewardAd = [[AdWhaleRewardAd alloc] init];
```
{% endtab %}
{% endtabs %}

#### 2. Delegate 설정

보상형 광고의 이벤트는 Delegate를 통해 전달됩니다.

{% tabs %}
{% tab title="Swift" %}
```swift
rewardAd.rewardDelegate = self
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
rewardAd.rewardDelegate = self;
```
{% endtab %}
{% endtabs %}

#### 3. 광고 요청

Delegate 설정이 완료되면 `load()`를 호출하여 보상형 광고를 요청합니다.\
이때 보상형 광고용으로 발급받은 Ad Unit ID를 전달해야 합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
rewardAd.load("리워드 광고 AD_UNIT_ID 입력")
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[rewardAd load:@"리워드 광고 AD_UNIT_ID 입력"];
```
{% endtab %}
{% endtabs %}

#### 4. 광고 표시

광고가 로드된 상태라면 `show()`를 호출하여 보상형 광고를 표시할 수 있습니다.\
이때 지정한 ViewController를 기준으로 전면 광고를 표시합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
rewardAd.show(self)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[rewardAd show:self];
```
{% endtab %}
{% endtabs %}

### 보상형 SSV(서버 사이드 검증) <a href="#id-2.-initialize" id="id-2.-initialize"></a>

보상형 SSV 는 서버에서 리워드 검증 및 지급을 제공하기 위한 기능입니다. 광고 네트워크가 퍼블리셔 서버로 직접 postback(콜백)을 보내며, ADWhale 은 이를 위해 `setUserId` / `setCustomData` 두 개의 API 를 제공합니다.

{% hint style="warning" %}
**지원 범위: AdMob / AdManager 만 지원합니다.**&#x20;

보상형(Rewarded)·보상형 전면(RewardedInterstitial) 광고에서 AdMob(SSV, Server-Side Verification) 으로만 동작합니다. 그 외 네트워크에서 두 메서드는 **무시(no-op)** 되며 아무 동작도 하지 않습니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
let customData: [String: String] = [
    "custom_test_key": "custom_test_value",
    "os": "iOS"
]
adWhaleMediationRewardAd.setCustomData(customData) // custom data (json 직렬화 문자열 처리)
adWhaleMediationRewardAd.setUserId("test_user_id") // 앱 사용자 id
```

{% hint style="info" %}
**호출 타이밍**

* `AdWhaleRewardAd()` 이후 `showAd()` 직전까지 **언제든** 호출할 수 있습니다.
* 여러 번 호출하면 **마지막 값이 적용**됩니다.
* `showAd()` 이후 호출은 반영되지 않습니다.

***

**대시보드 사전 설정 (필수)**

대시보드의 SSV callback URL 등록이 선행되지 않으면 postback 이 발생하지 않습니다. **AdMob / AdManager 대시보드에서 해당 보상형 ad unit 별로 SSV callback URL 을 먼저 등록**해야 합니다.

* AdMob 공식 가이드: [https://developers.google.com/admob/ios/ssv](https://developers.google.com/admob/ios/ssv?hl=ko)

***

**customData JSON 직렬화 주의**

AdMob SSV 의 `custom_data` 는 **단일 문자열** 필드입니다. AdWhale 은 전달받은 `Map<String,String>` 을 JSON 으로 직렬화해 싣습니다.

```swift
// 내부 처리 (참고)
String(data: JSONSerialization.data(withJSONObject: customData), encoding: .utf8)
```

* 퍼블리셔 서버는 postback 의 `custom_data` 값을 **JSON 으로 파싱**해야 key/value 를 얻을 수 있습니다.
* AdMob 이 `custom_data` 길이 상한을 공식적으로 명시하진 않지만, 실무상 **200자 정도를 넘기면 잘림**이 보고된 사례가 있으니 길이에 유의하세요.
{% endhint %}
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
NSDictionary<NSString *, NSString *> *customData = @{
    @"custom_test_key": @"custom_test_value",
    @"os": @"iOS"
};
[adWhaleMediationRewardAd setCustomData:customData]; // custom data (json 직렬화 문자열 처리)
[adWhaleMediationRewardAd setUserId:@"test_user_id"]; // 앱 사용자 id
```

{% hint style="info" %}
**호출 타이밍**

* `AdWhaleRewardAd()` 이후 `showAd()` 직전까지 **언제든** 호출할 수 있습니다.
* 여러 번 호출하면 **마지막 값이 적용**됩니다.
* `showAd()` 이후 호출은 반영되지 않습니다.

***

**대시보드 사전 설정 (필수)**

대시보드의 SSV callback URL 등록이 선행되지 않으면 postback 이 발생하지 않습니다. **AdMob / AdManager 대시보드에서 해당 보상형 ad unit 별로 SSV callback URL 을 먼저 등록**해야 합니다.

* AdMob 공식 가이드: [https://developers.google.com/admob/ios/ssv](https://developers.google.com/admob/ios/ssv?hl=ko)

***

**customData JSON 직렬화 주의**

AdMob SSV 의 `custom_data` 는 **단일 문자열** 필드입니다. AdWhale 은 전달받은 `Map<String,String>` 을 JSON 으로 직렬화해 싣습니다.

```objective-c
// 내부 처리 (참고)
[[NSString alloc] initWithData:[NSJSONSerialization dataWithJSONObject:customData
                                                               options:0
                                                                 error:nil]
                      encoding:NSUTF8StringEncoding]
```

* 퍼블리셔 서버는 postback 의 `custom_data` 값을 **JSON 으로 파싱**해야 key/value 를 얻을 수 있습니다.
* AdMob 이 `custom_data` 길이 상한을 공식적으로 명시하진 않지만, 실무상 **200자 정도를 넘기면 잘림**이 보고된 사례가 있으니 길이에 유의하세요.
{% endhint %}
{% endtab %}
{% endtabs %}

### Reward Delegate 설명

보상형 광고의 상태 변화는 Delegate를 통해 전달됩니다.

| Delegate 메서드                        | 설명                                 |
| ----------------------------------- | ---------------------------------- |
| adDidReceiveRewardAd                | 보상형 광고가 성공적으로 로드되었을 때 호출됩니다.       |
| adDidFailToReceiveRewardAdWithError | 보상형 광고 로드에 실패했을 때 호출됩니다.           |
| adWillPresentRewardAd               | 보상형 광고가 화면에 노출되었을 때 호출됩니다.         |
| didFailToPresentRewardAdWithError   | 보상형 광고 노출에 실패했을 때 호출됩니다.           |
| adDidDismissRewardAd                | 보상형 광고가 종료되었을 때 호출됩니다.             |
| adDidEarnReward                     | 사용자가 광고 시청을 완료하여 보상을 획득했을 때 호출됩니다. |

{% hint style="info" %}
보상형 광고는 사용자가 광고를 시청한 뒤\
**보상을 제공하는 전면 광고 유형**입니다.

보상형 광고를 사용하기 위해 `AdWhaleRewardAd` 객체를 생성한 뒤,\
Delegate를 설정하고 `load()`를 호출하여 광고를 요청합니다.

광고가 로드된 이후에는\
앱의 흐름에 맞는 시점에서 `show()`를 호출하여 광고를 표시할 수 있으며,\
보상 지급은 `adDidEarnReward` 콜백을 통해 처리합니다.
{% endhint %}

### 보상형 광고 구현 샘플

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    var rewardAd: AdWhaleRewardAd?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // Reward Ad Setting
        let reward = AdWhaleRewardAd()
        reward.rewardDelegate = self
        
        // Reward Ad Request
        reward.load("리워드 광고 AD_UNIT_ID 입력")
    }
    
    @IBAction func rewardAdShow(_ sender: UIButton) {
        rewardAd?.show(self)
    }
}

// MARK: RewardAd Delegate
extension ViewController: AdWhaleRewardDelegate {
    func adDidReceiveRewardAd(_ ad: AdWhaleSDK.AdWhaleRewardAd) {
        print("AdWhaleSample adDidReceiveRewardAd")
        rewardAd = ad
    }
    
    func adDidEarnReward(_ reward: AdWhaleSDK.AdWhaleReward) {
        print("AdWhaleSample \(reward.amount), amount \(reward.amount.doubleValue), reward type \(reward.type)")
    }
    
    func ad(_ ad: AdWhaleRewardAd, didFailToPresentRewardAdWithError error: Error) {
        print("AdWhaleSample didFailToPresentRewardAdWithError: \(error)")
    }
    
    func adDidFailToReceiveRewardAdWithError(_ error: Error) {
        print("AdWhaleSample adDidFailToReceiveRewardAdWithError: \(error)")
    }
    
    func adWillPresentRewardAd(_ ad: AdWhaleSDK.AdWhaleRewardAd) {
        print("AdWhaleSample adWillPresentRewardAd")
    }
    
    func adDidDismissRewardAd(_ ad: AdWhaleSDK.AdWhaleRewardAd) {
        print("AdWhaleSample adDidDismissRewardAd")
    }
}

```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "ViewController.h"
@import AdWhaleSDK;

@interface ViewController () <AdWhaleRewardDelegate>

@property (nonatomic, strong) AdWhaleRewardAd *rewardAd;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    // Reward Ad Setting
    AdWhaleRewardAd *reward = [[AdWhaleRewardAd alloc] init];
    reward.rewardDelegate = self;
    
    // Reward Ad Request
    [reward load:@"리워드 광고 AD_UNIT_ID 입력"];
}

- (IBAction)rewardAdShow:(id)sender {
    NSLog(@"reward Ad Show");
    [_rewardAd show:self];
}

#pragma mark - RewardAd Delegate

- (void)adDidReceiveRewardAd:(AdWhaleRewardAd *)ad {
    NSLog(@"AdWhaleSample adDidReceiveRewardAd");
    _rewardAd = ad;
}

- (void)adDidEarnReward:(AdWhaleReward *)reward {
    NSLog(@"AdWhaleSample %@, amount %f, reward type %@", reward.amount, reward.amount.doubleValue, reward.type);
}

- (void)ad:(AdWhaleRewardAd *)ad didFailToPresentRewardAdWithError:(NSError *)error {
    NSLog(@"AdWhaleSample didFailToPresentRewardAdWithError: %@", error);
}

- (void)adDidFailToReceiveRewardAdWithError:(NSError *)error {
    NSLog(@"AdWhaleSample adDidFailToReceiveRewardAdWithError: %@", error);
}

- (void)adWillPresentRewardAd:(AdWhaleRewardAd *)ad {
    NSLog(@"AdWhaleSample adWillPresentRewardAd");
}

- (void)adDidDismissRewardAd:(AdWhaleRewardAd *)ad {
    NSLog(@"AdWhaleSample adDidDismissRewardAd");
}

@end
```
{% endtab %}
{% endtabs %}







