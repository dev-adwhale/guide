# 배너

{% hint style="info" %}
* AdWhale SDK를 프로젝트에 추가 해야합니다.
* Banner Ad용으로 발급받은 Ad Unit ID를 사용합니다.
* 광고를 요청하기 전에 SDK 초기화를 진행합니다.
{% endhint %}

### BannerAd 설정

#### 1. 광고 단위 설정

배너 광고용으로 발급받은 `ad unit ID`를 사용하여 광고 단위를 설정하세요.

{% tabs %}
{% tab title="Swift" %}
```swift
bannerView.setAdUnitID("배너 광고 AD_UNIT_ID 입력")
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[_bannerView setAdUnitID:@"배너 광고 AD_UNIT_ID 입력"];
```
{% endtab %}
{% endtabs %}

#### 2. 배너 사이즈 설정

배너 광고의 크기를 설정합니다.

선택한 배너 사이즈에 맞춰 배너 뷰의 높이 및 레이아웃을 구성해야 합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
bannerView.setAdSize(.banner)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[_bannerView setAdSize:AdWhaleAdSizeBanner];
```
{% endtab %}
{% endtabs %}

| **AdWhaleAdSize(Swift)** | **AdWhaleAdSize(ObjC)**      | **Size (width \* height)** |
| ------------------------ | ---------------------------- | -------------------------- |
| .banner                  | AdWhaleAdSizeBanner          | 320 \*50                   |
| .largeBanner             | AdWhaleAdSizeLargeBanner     | 320 \* 100                 |
| .mediumRectangle         | AdWhaleAdSizeMediumRectangle | 300 \* 250                 |

#### 3. RootViewController 설정

배너 광고의 기준이 되는 ViewController를 설정합니다.

{% tabs %}
{% tab title="Swift" %}
```swift
bannerView.setRootViewController(self)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[_bannerView setRootViewController:self];
```
{% endtab %}
{% endtabs %}

#### 4. Delegate 설정

배너 광고의 이벤트는 **Delegate를 통해 전달**됩니다.

{% tabs %}
{% tab title="Swift" %}
```swift
bannerView.setDelegate(self)
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[_bannerView setDelegate:self];
```
{% endtab %}
{% endtabs %}

#### 5. 광고 요청

설정된 정보(AdSize, Ad Unit ID 등)를 기준으로\
배너 광고를 요청(load)합니다.

광고 요청 결과는 Delegate 메서드를 통해 전달됩니다.

{% tabs %}
{% tab title="Swift" %}
```swift
bannerView.load()
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
[self.bannerView load];
```
{% endtab %}
{% endtabs %}

### Banner Delegate 설명

배너 광고의 상태 변화는 Delegate를 통해 전달됩니다.

| Delegate 메서드                  | 설명                                |
| ----------------------------- | --------------------------------- |
| bannerViewDidReceiveAd        | 배너 광고가 성공적으로 로드되었을 때 호출됩니다.       |
| didFailToReceiveAdWithError   | 배너 광고 로드에 실패했을 때 호출됩니다.           |
| bannerViewWillPresentScreen   | 배너 광고 클릭으로 전체 화면이 표시되기 직전에 호출됩니다. |
| bannerViewDidRecordImpression | 배너 광고가 화면에 노출되었을 때 호출됩니다.         |
| bannerViewWillDismissScreen   | 배너 광고로 열린 화면이 닫히기 직전에 호출됩니다.      |
| bannerViewDidDismissScreen    | 배너 광고로 열린 화면이 완전히 닫힌 후 호출됩니다.     |

{% hint style="info" %}
배너 광고는 화면 상단 또는 하단에 **뷰(View) 형태로 표시**됩니다.\
배너 광고를 사용하기 위해 `AdWhaleBannerAd` 뷰를 생성하거나 Storyboard에 배치한 뒤,\
Ad Unit ID와 Delegate를 설정하고 `load()`를 호출하여 광고를 요청합니다.

배너 광고는 광고가 로드된 이후,\
배너 뷰가 포함된 화면이 유지되는 동안 지속적으로 노출됩니다.
{% endhint %}

### 배너 광고 구현 샘플

#### 1. Storyboard 방식

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    @IBOutlet var bannerView: AdWhaleBannerAd!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // BannerView Setting
        bannerView.setAdUnitID("배너 광고 AD_UNIT_ID 입력")
        bannerView.setAdSize(.banner)
        bannerView.setRootViewController(self)
        bannerView.setDelegate(self)
        
        // Banner Ad Request
        bannerView.load()
    }
}

// MARK: BannerAd Delegate
extension ViewController: AdWhaleBannerDelegate {
    func bannerViewDidReceiveAd(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidReceiveAd")
    }
    
    func bannerView(_ bannerView: AdWhaleSDK.AdWhaleBannerAd, didFailToReceiveAdWithError error: Error) {
        print("ViewController didFailToReceiveAdWithError: \(error.localizedDescription)")
    }
    
    func bannerViewDidRecordImpression(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidRecordImpression")
    }
    
    func bannerViewWillPresentScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewWillPresentScreen")
    }
    
    func bannerViewWillDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewWillDismissScreen")
    }
    
    func bannerViewDidDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidDismissScreen")
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "ViewController.h"
@import AdWhaleSDK;

@interface ViewController () <AdWhaleBannerDelegate>

@property (nonatomic, strong) IBOutlet AdWhaleBannerAd *bannerView;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    // BannerView Setting
    [_bannerView setAdUnitID:@"배너 광고 AD_UNIT_ID 입력"];
    [_bannerView setAdSize:AdWhaleAdSizeBanner];
    [_bannerView setRootViewController:self];
    [_bannerView setDelegate:self];
    
    // Banner Ad Request
    [self.bannerView load];
}

#pragma mark - BannerAd Delegate
- (void)bannerViewDidReceiveAd:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidReceiveAd");
}

- (void)bannerView:(AdWhaleBannerAd *)bannerView didFailToReceiveAdWithError:(NSError *)error {
    NSLog(@"ViewController didFailToReceiveAdWithError: %@", error.localizedDescription);
}

- (void)bannerViewDidRecordImpression:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidRecordImpression");
}

- (void)bannerViewWillPresentScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewWillPresentScreen");
}

- (void)bannerViewWillDismissScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewWillDismissScreen");
}

- (void)bannerViewDidDismissScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidDismissScreen");
}

@end
```
{% endtab %}
{% endtabs %}



#### 2. 프로그램 코드 방식

{% tabs %}
{% tab title="Swift" %}
```swift
import UIKit
import AdWhaleSDK

class ViewController: UIViewController {
    var bannerView: AdWhaleBannerAd?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let frame = CGRect(x: 0,
                           y: 500,
                           width: UIScreen.main.bounds.width,
                           height: 50)
        
        bannerView = AdWhaleBannerAd(frame: frame)
        view.addSubview(bannerView)
        
        // BannerView Setting
        bannerView?.setAdSize(.banner)
        bannerView?.setAdUnitID("배너 광고 AD_UNIT_ID 입력")
        bannerView?.setRootViewController(self)
        bannerView?.setDelegate(self)
        
        // Banner Ad Request
        bannerView.load()
    }
}

// MARK: BannerAd Delegate
extension ViewController: AdWhaleBannerDelegate {
    func bannerViewDidReceiveAd(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidReceiveAd")
    }
    
    func bannerView(_ bannerView: AdWhaleSDK.AdWhaleBannerAd, didFailToReceiveAdWithError error: Error) {
        print("ViewController didFailToReceiveAdWithError: \(error.localizedDescription)")
    }
    
    func bannerViewDidRecordImpression(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidRecordImpression")
    }
    
    func bannerViewWillPresentScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewWillPresentScreen")
    }
    
    func bannerViewWillDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewWillDismissScreen")
    }
    
    func bannerViewDidDismissScreen(_ bannerView: AdWhaleSDK.AdWhaleBannerAd) {
        print("ViewController bannerViewDidDismissScreen")
    }
}
```
{% endtab %}

{% tab title="Objective-C" %}
```objective-c
#import "ViewController.h"
@import AdWhaleSDK;

@interface ViewController () <AdWhaleBannerDelegate>

@property (nonatomic, strong) AdWhaleBannerAd *bannerView;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    // BannerView Create
    CGRect frame = CGRectMake(0,
                              500,
                              UIScreen.mainScreen.bounds.size.width,
                              50);
    self.bannerView = [[AdWhaleBannerAd alloc] initWithFrame:frame];
    [self.view addSubview:self.bannerView];
    
    // BannerView Setting
    [_bannerView setAdSize:AdWhaleAdSizeBanner];
    [_bannerView setAdUnitID:@"배너 광고 AD_UNIT_ID 입력"];
    [_bannerView setRootViewController:self];
    [_bannerView setDelegate:self];
    
    // Banner Ad Request
    [self.bannerView load];
}

#pragma mark - BannerAd Delegate
- (void)bannerViewDidReceiveAd:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidReceiveAd");
}

- (void)bannerView:(AdWhaleBannerAd *)bannerView didFailToReceiveAdWithError:(NSError *)error {
    NSLog(@"ViewController didFailToReceiveAdWithError: %@", error.localizedDescription);
}

- (void)bannerViewDidRecordImpression:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidRecordImpression");
}

- (void)bannerViewWillPresentScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewWillPresentScreen");
}

- (void)bannerViewWillDismissScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewWillDismissScreen");
}

- (void)bannerViewDidDismissScreen:(AdWhaleBannerAd *)bannerView {
    NSLog(@"ViewController bannerViewDidDismissScreen");
}

@end
```
{% endtab %}
{% endtabs %}
