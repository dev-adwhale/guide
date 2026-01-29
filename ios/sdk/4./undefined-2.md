# 전면

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Interstitial Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

### InterstitialAd 설정

#### 1. 광고 객체 생성

전면 광고를 사용하기 위해 `AdWhaleInterstitialAd` 객체를 생성합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
let interstitialAd = AdWhaleInterstitialAd()
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
AdWhaleInterstitialAd *interstitial = [[AdWhaleInterstitialAd alloc] init];
```
{% endtab %}
{% endtabs %}

#### 2. Delegate 설정

전면 광고의 이벤트는 Delegate를 통해 전달됩니다.

{% tabs %}
{% tab title="Swift" %}
```swift
interstitialAd.interstitialDelegate = self
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
interstitial.interstitialDelegate = self;
```
{% endtab %}
{% endtabs %}

#### 3. 광고 요청

Delegate 설정이 완료되면 `load()`를 호출하여 전면 광고를 요청합니다.\
이때 전면 광고용으로 발급받은 Ad Unit ID를 전달해야 합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
interstitialAd.load("전면 광고 AD_UNIT_ID 입력")
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[interstitial load:@"전면 광고 AD_UNIT_ID 입력"];
```
{% endtab %}
{% endtabs %}

#### 4. 광고 표시

광고가 로드된 상태라면 `show()`를 호출하여 전면 광고를 표시할 수 있습니다.\
이때 지정한 ViewController를 기준으로 전면 광고를 표시합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
interstitialAd.show(self)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[interstitialAd show:self];
```
{% endtab %}
{% endtabs %}

### Interstitial Delegate 설명

전면 광고의 상태 변화는 Delegate를 통해 전달됩니다.

| Delegate 메서드                              | 설명                          |
| ----------------------------------------- | --------------------------- |
| adDidReceiveInterstitialAd                | 전면 광고가 성공적으로 로드되었을 때 호출됩니다. |
| adDidFailToReceiveInterstitialAdWithError | 전면 광고 로드에 실패했을 때 호출됩니다.     |
| adWillPresentInterstitialAd               | 전면 광고가 화면에 노출되었을 때 호출됩니다.   |
| didFailToPresentInterstitialAdWithError   | 전면 광고 노출에 실패했을 때 호출됩니다.     |
| adDidDismissInterstitialAd                | 전면 광고가 종료되었을 때 호출됩니다.       |

{% hint style="info" %}
전면 광고는 **특정 시점에 화면을 가득 채워 노출되는 광고 유형**입니다.\
전면 광고를 사용하기 위해 `AdWhaleInterstitialAd` 객체를 생성한 뒤,\
Delegate를 설정하고 `load()`를 호출하여 광고를 요청합니다.

광고가 로드된 이후에는\
앱의 흐름에 맞는 시점에서 `show()`를 호출하여 광고를 표시할 수 있습니다.
{% endhint %}

### 전면 광고 구현 샘플

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    var interstitialAd: AdWhaleInterstitialAd?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // Interstitial Ad Setting
        let interstitialAd = AdWhaleInterstitialAd()
        interstitialAd?.interstitialDelegate = self
        
        // Interstitial Ad Request
        interstitialAd?.load("전면 광고 AD_UNIT_ID 입력")
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
    
    // Interstitial Ad Setting
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







