# 전면

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Interstitial Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    var interstitialAd: AdWhaleInterstitialAd?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        let interstitialAd = AdWhaleInterstitialAd()
        interstitialAd.interstitialDelegate = self
        
        // Interstitial Ad Request
        interstitialAd.load("전면 광고 AD_UNIT_ID 입력")
    }
    
    @IBAction func interstitialAdShow(_ sender: UIButton) {
        interstitialAd?.show(self)
    }
}

// MARK: InterstitialAd Delegate
extension ViewController: AdWhaleInterstitialDelegate {
    func adDidReceiveInterstitialAd(_ ad: AdWhaleSDK.AdWhaleInterstitialAd) {
        print("adWhale Sample adDidReceiveInterstitialAd")
        interstitialAd = ad
    }
    
    func adDidFailToReceiveInterstitialAdWithError(_ error: Error) {
        print("adWhale Sample adDidFailToReceiveInterstitialAdWithError")
    }
    
    func ad(_ ad: AdWhaleSDK.AdWhaleInterstitialAd, didFailToPresentInterstitialAdWithError error: Error) {
        print("adWhale Sample didFailToPresentInterstitialAdWithError")
    }
    
    func adWillPresentInterstitialAd(_ ad: AdWhaleSDK.AdWhaleInterstitialAd) {
        print("adWhale Ad will present Interstitial ad.")
    }
    
    func adDidDismissInterstitialAd(_ ad: AdWhaleSDK.AdWhaleInterstitialAd) {
        print("adWhale Ad did dismiss Interstitial ad.")
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "ViewController.h"
@import AdWhaleSDK;

@interface ViewController () <AdWhaleInterstitialDelegate>

@property (nonatomic, strong) AdWhaleInterstitialAd *interstitialAd;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    AdWhaleInterstitialAd *interstitial = [[AdWhaleInterstitialAd alloc] init];
    interstitial.interstitialDelegate = self;
    
    // Interstitial Ad Request
    [interstitial load:@"전면 광고 AD_UNIT_ID 입력"];
}

- (IBAction)interstitialAdShow:(id)sender {
    NSLog(@"interstitial Ad Show");
    [interstitialAd show:self];
}

#pragma mark - InterstitialAd Delegate

- (void)adDidReceiveInterstitialAd:(AdWhaleInterstitialAd *)ad {
    NSLog(@"adWhale Sample adDidReceiveInterstitialAd");
    interstitialAd = ad;
}

- (void)adDidFailToReceiveInterstitialAdWithError:(NSError *)error {
    NSLog(@"adWhale Sample adDidFailToReceiveInterstitialAdWithError");
}

- (void)ad:(AdWhaleInterstitialAd *)ad didFailToPresentInterstitialAdWithError:(NSError *)error {
    NSLog(@"adWhale Sample didFailToPresentInterstitialAdWithError: %@", error);
}

- (void)adWillPresentInterstitialAd:(AdWhaleInterstitialAd *)ad {
    NSLog(@"adWhale Ad will present Interstitial ad.");
}

- (void)adDidDismissInterstitialAd:(AdWhaleInterstitialAd *)ad {
    NSLog(@"adWhale Ad did dismiss Interstitial ad.");
}

@end
```
{% endtab %}
{% endtabs %}







