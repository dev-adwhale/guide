# 리워드

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Reward Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    var rewardAd: AdWhaleRewardAd?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let reward = AdWhaleRewardAd()
        reward.rewardDelegate = self
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







