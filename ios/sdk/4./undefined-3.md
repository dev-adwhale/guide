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







